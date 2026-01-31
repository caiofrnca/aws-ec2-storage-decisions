### Immutable Infrastructure

Immutable infrastructure is a pattern where servers are never modified after deployment.

Instead of patching or changing running instances, a new machine image (AMI) is built and new instances are launched from it. Old instances are then terminated.

This approach reduces configuration drift, improves repeatability, and simplifies rollback by allowing teams to revert to a known-good image.
