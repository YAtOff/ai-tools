# Architecture Patterns Reference

## Service Pattern

```python
@transaction.atomic
def entity_create(
    *,  # Keyword-only marker
    required_field: str,
    optional_field: str | None = None,
) -> Entity:
    """Create entity.

    Args:
        required_field: Description
        optional_field: Description

    Returns:
        Created entity

    Raises:
        EntityValidationError: If validation fails
    """
    # 1. Validation
    if not required_field:
        raise EntityValidationError("...")

    # 2. Create
    entity = Entity(...)

    # 3. Validate and save
    try:
        entity.full_clean()
        entity.save()
    except ValidationError as e:
        raise EntityValidationError(str(e)) from e

    # 4. Log
    logger.info("Entity created", extra={"entity_id": str(entity.id)})

    return entity
```

**Checklist**:
- [ ] `@transaction.atomic`
- [ ] Keyword-only args (`*,`)
- [ ] Type hints
- [ ] Docstring (Args/Returns/Raises)
- [ ] `full_clean()` before `save()`
- [ ] Custom exceptions (not Django's)
- [ ] Structured logging with `extra={}`
- [ ] Returns entity

## Selector Pattern

```python
def entity_list(
    *,
    filters: dict | None = None,
    user: User | None = None,
) -> QuerySet[Entity]:
    """List entities with filtering."""
    filters = filters or {}
    qs = Entity.objects.all()

    # Optimize relations
    qs = qs.select_related('foreign_key')
    qs = qs.prefetch_related(
        Prefetch('reverse_rel', queryset=...),
    )

    # Access control
    if not user or not user.is_authenticated:
        qs = qs.filter(is_public=True)

    # Filters
    if 'category' in filters:
        qs = qs.filter(category=filters['category'])

    return qs.order_by('-created_at')
```

**Checklist**:
- [ ] Returns QuerySet (not list)
- [ ] `select_related()` for FKs
- [ ] `prefetch_related()` for reverse relations
- [ ] Access control applied
- [ ] No business logic
- [ ] Type hints on return

## API Pattern

```python
class EntityListCreateApi(APIView):
    permission_classes = [...]
    throttle_classes = [...]

    class InputSerializer(serializers.Serializer):
        """Input for create."""
        field = serializers.CharField()

    class OutputSerializer(serializers.ModelSerializer):
        """Output format."""
        class Meta:
            model = Entity
            fields = [...]

    @extend_schema(...)
    def post(self, request):
        # 1. Validate
        serializer = self.InputSerializer(data=request.data)
        serializer.is_valid(raise_exception=True)

        # 2. Service call
        entity = services.entity_create(**serializer.validated_data)

        # 3. Output
        output = self.OutputSerializer(entity)
        return Response(output.data, status=201)
```

**Checklist**:
- [ ] Inherits APIView (not generics)
- [ ] Nested Input/Output serializers
- [ ] Thin methods (<100 lines)
- [ ] Services for writes, selectors for reads
- [ ] `@extend_schema` for OpenAPI
- [ ] Permission classes
- [ ] Throttle classes
- [ ] No business logic
