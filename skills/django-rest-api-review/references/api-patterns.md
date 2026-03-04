# API Patterns Reference

## RESTful Principles

**URL Patterns**:
- ✅ `/resources/` (collection)
- ✅ `/resources/{id}/` (instance)
- ✅ `/resources/{id}/publish` (action)
- ❌ `/create-resource/` (verb in URL)
- ❌ `/getResource/` (wrong casing, verb)

**HTTP Methods & Status Codes**:

| Method | Purpose | Success | Error |
|--------|---------|---------|-------|
| GET | Read | 200 | 404 |
| POST | Create | 201 | 400, 409 |
| PUT | Replace | 200 | 400, 404 |
| PATCH | Update | 200 | 400, 404 |
| DELETE | Remove | 204 | 404 |

**Security**: Return 404 (not 403) for private resources to prevent info leakage.

## API Versioning

**Recommendation (default)**: URI path versioning.

- ✅ Include the API version in the URL path: `/api/v1/...`
- ✅ Keep version segments stable and explicit (`v1`, `v2`, ...)

**When to increment versions**:

- ✅ Breaking changes require a new version:
    - Removing or renaming fields
    - Changing response shape/semantics
    - Altering validation/permissions in a way that breaks existing clients
    - Changing meaning of status codes or error formats
- ✅ Non-breaking changes do **not** require a new version:
    - Adding new optional fields
    - Adding new endpoints
    - Adding new query parameters (optional)

## Request/Response Schemas

```python
class EntityApi(APIView):
    class InputSerializer(serializers.Serializer):
        """Create entity request."""
        title = serializers.CharField(
            min_length=1,
            max_length=200,
            help_text="Entity title"
        )
        tags = serializers.ListField(
            child=serializers.CharField(max_length=50),
            min_length=2,
            help_text="At least 2 tags required"
        )

        class Meta:
            ref_name = "EntityCreateInput"  # OpenAPI clarity

    class OutputSerializer(serializers.ModelSerializer):
        """Entity response."""
        owner = OwnerSerializer()  # Nested for relations

        class Meta:
            model = Entity
            fields = ['id', 'title', 'owner', 'created_at']
            ref_name = "EntityOutput"
```

**Checklist**:
- [ ] Separate Input/Output serializers
- [ ] Nested serializers for relations
- [ ] Field validation (min/max_length, choices)
- [ ] `help_text` on fields
- [ ] `ref_name` for OpenAPI

## Error Handling

**RFC 7807 Problem Details Format**:

```python
# ✅ Standardized error response
{
    "error": "validation_error",
    "message": "At least 2 tags required",
    "details": {
        "field": "tags",
        "constraint": "min_length"
    }
}
```

**Exception Hierarchy**:

```python
class DomainError(Exception):
    """Base domain error."""
    pass

class EntityValidationError(DomainError):
    """Entity validation failed."""
    pass

class EntityNotFoundError(DomainError):
    """Entity not found."""
    pass
```

**Checklist**:
- [ ] RFC 7807 format
- [ ] Custom exception hierarchy
- [ ] Specific error messages
- [ ] Field-level validation errors
- [ ] No info leakage (404 for 403)

## OpenAPI Documentation

```python
from drf_spectacular.utils import extend_schema, OpenApiExample

class EntityListApi(APIView):
    @extend_schema(
        operation_id="entity_list",
        description="List all entities",
        responses={200: EntityOutputSerializer(many=True)},
        tags=["entities"],
        examples=[
            OpenApiExample(
                "Success",
                value=[{"id": "...", "title": "Example"}],
            )
        ]
    )
    def get(self, request):
        ...
```

**Checklist**:
- [ ] `@extend_schema` on all methods
- [ ] `operation_id` set
- [ ] `description` provided
- [ ] `responses` documented
- [ ] `tags` for grouping
- [ ] `examples` where helpful
