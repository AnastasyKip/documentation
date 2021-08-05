---
description: API to validate resources
---

# Validation API

### Batch Validation `draft`

#### aidbox.validation/batch-validation

It may happen that you updated your profiles when data is already in your database or you want to do efficiently load a batch of data and validate it later. You can validate your existing data with our new rpc `aidbox/batch-validation`:

```yaml
POST /rpc
content-type: text/yaml

method: aidbox/batch-validation
params:
  resource: Patient
  id: pt-validation-run-1
  # you can limit number of resources to validate
  limit: 100 
  # you can stop process on specific number of invalid resources
  errorsThreshold: 10 
  # run validation asynchronously
  async: true
  # specify profiles to validate
  profiles: ['profile-url-1', 'profile-url-2']
  # specify zen schemas to validation
  schemas: ['myapp/Patient', 'us-core.patient/Patient']
  
  
# response
status: 200
result:
  valid: 1543
  invalid: 0
  duration: 3293
  resource: SearchParameter
  id: s2
```

#### aidbox.validation/clear-batch-validation

#### BatchValidationRun & BatchValidationError Resources
