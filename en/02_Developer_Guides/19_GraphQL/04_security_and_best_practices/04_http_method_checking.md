---
title: Strict HTTP method checking
summary: Ensure requests are GET or POST
---

# Security & best practices

[CHILDREN asList]

## Strict HTTP Method Checking

According to GraphQL best practices, mutations should be done over `POST`, while queries have the option
to use either `GET` or `POST`. By default, this module enforces the `POST` request method for all mutations.

To disable that requirement, you can remove the [`HTTPMethodMiddleware`](api:SilverStripe\GraphQL\Middleware\HTTPMethodMiddleware)
from the [`QueryHandler`](api:SilverStripe\GraphQL\QueryHandler\QueryHandler).

```yaml
SilverStripe\GraphQL\QueryHandler\QueryHandlerInterface.default:
  class: SilverStripe\GraphQL\QueryHandler\QueryHandler
  properties:
    Middlewares:
      httpMethod: false
```

### Further reading

[CHILDREN]
