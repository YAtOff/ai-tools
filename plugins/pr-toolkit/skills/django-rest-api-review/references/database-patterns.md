# Database Patterns Reference

## Model Structure

```python
class Entity(models.Model):
    id = models.UUIDField(primary_key=True, default=uuid.uuid4, editable=False, db_index=True)

    # TextChoices for enums
    class Status(models.TextChoices):
        DRAFT = 'DRAFT', 'Draft'
        PUBLISHED = 'PUBLISHED', 'Published'

    status = models.CharField(max_length=20, choices=Status.choices, default=Status.DRAFT)
    tags = ArrayField(models.CharField(max_length=50), default=list)  # Not JSONField

    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)

    class Meta:
        db_table = 'entities'
        db_table_comment = 'Entity description'  # Django 5.1+
```

**Checklist**:
- [ ] UUID PK with `db_index=True`
- [ ] TextChoices for enums
- [ ] ArrayField for lists (not JSONField)
- [ ] Timestamps: `auto_now_add`, `auto_now`
- [ ] `db_table` explicit
- [ ] `db_table_comment` (Django 5.1+)

## Indexes

```python
indexes = [
    # FK index
    models.Index(fields=['owner_profile']),

    # GIN for ArrayField
    GinIndex(fields=['tags'], name='idx_tags', opclasses=['array_ops']),

    # GIN for JSONField
    GinIndex(fields=['metadata'], name='idx_metadata'),

    # Partial index
    models.Index(
        fields=['-created_at'],
        condition=Q(status='PUBLISHED'),
        name='idx_published',
    ),

    # BRIN for timestamps (large tables)
    BrinIndex(fields=['created_at'], name='idx_created_brin'),

    # Composite
    models.Index(fields=['owner_profile', 'status'], name='idx_owner_status'),
]
```

**Checklist**:
- [ ] All FK fields indexed (explicit `db_index=True` for UUIDs)
- [ ] GIN for ArrayField with `array_ops`
- [ ] GIN for JSONField
- [ ] Partial indexes for filtered queries
- [ ] BRIN for timestamps in large tables
- [ ] Composite for multi-column queries

## Constraints

```python
constraints = [
    # Timestamp ordering
    models.CheckConstraint(
        check=Q(updated_at__gte=F('created_at')),
        name='updated_after_created',
    ),

    # Positive values
    models.CheckConstraint(
        check=Q(count__isnull=True) | Q(count__gte=1),
        name='positive_count',
    ),

    # Self-reference prevention
    models.CheckConstraint(
        check=~Q(parent=F('id')),
        name='no_self_parent',
    ),

    # Uniqueness
    models.UniqueConstraint(
        fields=['owner', 'slug'],
        name='unique_owner_slug',
    ),
]
```

**Checklist**:
- [ ] CHECK for business rules
- [ ] UNIQUE where needed
- [ ] Self-referential prevention
- [ ] Timestamp ordering

## Invariant Enforcement

For each invariant from domain model JSON, verify enforcement:

| Invariant Type | Enforcement Location |
|---------------|---------------------|
| Data format | DB constraint (CHECK) |
| Uniqueness | DB constraint (UNIQUE) |
| Referential | DB constraint (FK) |
| Cross-field | Model `clean()` method |
| Business logic | Service layer |

**CRITICAL**: Report any undocumented/unenforced invariants.

## Concurrency Protection

```python
# ✅ Atomic counter update
Resource.objects.filter(pk=pk).update(
    view_count=F('view_count') + 1
)
resource.refresh_from_db(fields=['view_count'])

# ❌ Race condition
resource.view_count += 1
resource.save()

# ✅ Resource allocation with locking
with transaction.atomic():
    quota = Quota.objects.select_for_update().get(pk=pk)
    if quota.remaining > 0:
        quota.remaining = F('remaining') - 1
        quota.save(update_fields=['remaining'])

# ✅ JSONB atomic update
Entity.objects.filter(pk=pk).update(
    metadata=Func(
        F('metadata'),
        Value('key'),
        Value('"value"'),
        function='jsonb_set',
    )
)
```

**Checklist**:
- [ ] F() for counter updates
- [ ] `select_for_update()` for allocation
- [ ] `refresh_from_db()` after atomic updates
- [ ] `jsonb_set` for JSON updates
