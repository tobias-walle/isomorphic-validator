# Isomorphic Validator

🚀 Share your form validator between client and server

**1. Define your validation schema**

```typescript
import { ValidationSchemaMapping } from 'isomorphic-validator';

const validationSchema: ValidationSchemaMapping<keyof MyObject> = {
  username: {
    required: {},
    limitLength: {
      min: 8,
      max: 30
    }
  },
  email: {
    isEmail: {},
  },
  age: {
    isNumber: {},
    inRange: {
      min: 0,
      max: 150
    }
  }
}
```

**2. Validate**
```typescript
import { createValueValidator, ValueValidatorConfig } from 'isomorphic-validator';

const myObject: MyObject = {
  username: 'user',
  email: 'invalidEmail',
  age: -10
}

// Create config or just leave empty to use defaults
const config: ValueValidatorConfig = {
  errorMapping: {
    required: {
      notDefined: 'This field is required'
    },
    limitLength: {
      underMin: ({params: {min}}) => `The minimal length is ${min}`,
      overMax: ({params: {max}}) => `The maximum length is ${max}`
    },
    isEmail: {
      notValid: 'The email is not valid'
    },
    isNumber: {
      notValid: 'This field has to be a number'
    },
    inRange: {
      underMin: ({params: {min}, meta: {name}}) => `The ${name} has to be at least ${min}.`,
      overMax: ({params: {max}, meta: {name}}) => `The ${name} has to be smaller than ${max}.`
    }
  }
}

const valueValidator = createValueValidator(config)(schema);
const errors = valueValidator(myObject);
/*
Returns:
{
  username: {
    limitLength: 'The minimal length is 8',
  },
  email: {
    isEmail: 'The email is not valid',
  },
  age: {
    inRange: 'The age has to be at least 0'
  }
}
 */
```

**3. Share**

As the schema is serializable you can easily share it between the client and the server.
Isomorphic makes no restrictions which technology you use for that.

