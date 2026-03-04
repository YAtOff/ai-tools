# Testing Patterns Reference

## Test Structure

```
app/
├── tests/
│   ├── __init__.py
│   ├── test_models.py
│   ├── test_services.py
│   ├── test_selectors.py
│   └── test_apis.py
```

**Naming**: `test_<operation>_<scenario>`

## Test Entity Builders

```python
class ResourceBuilder:
    """Fluent builder for Resource entities."""

    def __init__(self, owner_profile: Profile):
        self._owner = owner_profile
        self._title = fake.sentence()[:50]
        self._status = ResourceStatus.DRAFT
        self._used = False

    def with_title(self, title: str) -> 'ResourceBuilder':
        self._title = title
        return self

    def with_category(self, category: ResourceCategory) -> 'ResourceBuilder':
        self._category = category
        return self

    def published(self) -> 'ResourceBuilder':
        self._status = ResourceStatus.PUBLISHED
        return self

    def create(self) -> Resource:
        if self._used:
            raise RuntimeError("Builder already used")
        self._used = True

        resource = Resource(
            owner_profile=self._owner,
            title=self._title,
            status=self._status,
        )
        resource.full_clean()
        resource.save()
        return resource
```

**Checklist**:
- [ ] Fluent interface (chainable)
- [ ] Single-use (RuntimeError on reuse)
- [ ] Faker for realistic data
- [ ] Sensible defaults
- [ ] Calls `full_clean()` + `save()`

## Service Tests

```python
@pytest.mark.django_db
class TestEntityCreate:
    def test_success(self, profile):
        entity = services.entity_create(
            owner=profile,
            title="Test Entity",
        )

        assert entity.title == "Test Entity"
        assert entity.status == EntityStatus.DRAFT
        assert entity.owner == profile

    def test_validation_error_empty_title(self, profile):
        with pytest.raises(EntityValidationError) as exc:
            services.entity_create(
                owner=profile,
                title="",
            )
        assert "title" in str(exc.value)

    def test_invariant_enforced(self, profile):
        """Verify business invariant."""
        with pytest.raises(EntityValidationError):
            services.entity_create(
                owner=profile,
                start_date=date.today(),
                end_date=date.today() - timedelta(days=1),  # Invalid
            )
```

**Checklist**:
- [ ] All business logic paths covered
- [ ] Invariants verified
- [ ] Error cases tested
- [ ] Edge cases tested

## Selector Tests

```python
@pytest.mark.django_db
class TestEntityList:
    def test_returns_queryset(self, profile):
        result = selectors.entity_list(user=profile.user)
        assert isinstance(result, QuerySet)

    def test_n_plus_one_prevented(self, profile, django_assert_num_queries):
        # Create entities with relations
        for _ in range(5):
            EntityBuilder(owner=profile).create()

        # Should be constant queries regardless of count
        with django_assert_num_queries(2):  # 1 entities + 1 prefetch
            list(selectors.entity_list(user=profile.user))

    def test_access_control_public_only(self):
        """Anonymous users see only public."""
        result = selectors.entity_list(user=None)
        assert all(e.is_public for e in result)
```

**Checklist**:
- [ ] Returns QuerySet
- [ ] N+1 prevention verified
- [ ] Access control tested
- [ ] Filters tested

## API Tests

```python
@pytest.mark.django_db
class TestEntityCreateApi:
    def test_success(self, api_client, profile):
        api_client.force_authenticate(profile.user)

        response = api_client.post('/api/entities/', {
            'title': 'Test Entity',
        })

        assert response.status_code == 201
        assert response.data['title'] == 'Test Entity'

    def test_unauthenticated(self, api_client):
        response = api_client.post('/api/entities/', {
            'title': 'Test',
        })
        assert response.status_code == 401

    def test_validation_error(self, api_client, profile):
        api_client.force_authenticate(profile.user)

        response = api_client.post('/api/entities/', {
            'title': '',  # Invalid
        })

        assert response.status_code == 400
        assert 'title' in response.data

    def test_permission_denied_returns_404(self, api_client, other_user):
        """Private resources return 404 to hide existence."""
        api_client.force_authenticate(other_user)

        response = api_client.get('/api/entities/private-uuid/')

        assert response.status_code == 404  # Not 403
```

**Checklist**:
- [ ] Success cases
- [ ] Authentication required
- [ ] Permission checks
- [ ] Validation errors
- [ ] 404 for private (not 403)
