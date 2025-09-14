# Angular to React Codemod

A comprehensive codemod tool for converting Angular applications to React. This tool automatically transforms Angular components, services, directives, pipes, and modules into their React equivalents.

## Features

- 🔄 **Component Transformation**: Converts Angular components to React functional components with hooks
- 🎯 **Template to JSX**: Transforms Angular templates to JSX with proper event handling and data binding
- 🛠️ **Service to Hook**: Converts Angular services to React custom hooks and context providers
- 📦 **Directive Conversion**: Transforms Angular directives to React hooks and higher-order components
- 🔧 **Pipe Transformation**: Converts Angular pipes to React utility functions and hooks
- 🚦 **Routing Migration**: Transforms Angular Router to React Router
- 🧪 **Test Generation**: Automatically generates Jest/React Testing Library tests
- 📁 **Project Structure**: Creates complete React project structure with build configuration

## Installation

```bash
npm install -g angular-to-react-codemod
```

Or use directly with npx:

```bash
npx angular-to-react-codemod transform --source ./src --output ./react-app
```

## Usage

### Transform an Angular Project

```bash
ng2react transform --source ./src --output ./react-output --typescript
```

## NPM Scripts Reference

The following scripts are available to help you work with the codemod:

### Core Scripts
- **`npm test`** - Run Jest test suite to validate codemod functionality
- **`npm run test:watch`** - Run tests in watch mode with coverage reporting for development
- **`npm run build`** - Execute the main transformation engine (direct node execution)
- **`npm run lint`** - Run ESLint on source code with automatic fixing of style issues
- **`npm run validate`** - Run both linting and testing to ensure code quality

### CLI Wrapper Scripts
- **`npm run transform`** - Quick access to transform command (add `-- --source ./src --output ./react-app`)
- **`npm run analyze`** - Quick access to analyze command (add `-- --source ./src`)
- **`npm run init-react`** - Initialize a new React project structure (add `-- --output ./my-app`)
- **`npm run demo`** - Run built-in test examples to see the codemod in action
- **`npm start`** - Show CLI help and available commands
- **`npm run dev`** - Development mode with auto-restart on file changes (requires nodemon)

### Utility Scripts
- **`npm run example`** - Run the example transformation scenarios
- **`npm run clean`** - Remove generated output directories (tests/react-output, examples/react-output)

### Usage Examples

```bash
# Run tests with coverage
npm run test:watch

# Transform an Angular project
npm run transform -- --source ./my-angular-app/src --output ./my-react-app --typescript

# Analyze project structure first
npm run analyze -- --source ./my-angular-app/src

# Clean up generated files
npm run clean

# Validate code quality
npm run validate
```

### Analyze Angular Project

```bash
ng2react analyze --source ./src
```

### Initialize New React Project

```bash
ng2react init --output ./my-react-app --typescript
```

### Run Test Examples

```bash
ng2react test
```

## Command Options

### Transform Command

- `--source, -s`: Source directory containing Angular files (default: `./src`)
- `--output, -o`: Output directory for React files (default: `./react-output`)
- `--typescript, -t`: Generate TypeScript files
- `--no-tests`: Skip test file generation
- `--preserve-structure`: Preserve original directory structure
- `--react-version`: Target React version (default: `18`)
- `--dry-run`: Show what would be transformed without making changes

### Analyze Command

- `--source, -s`: Source directory to analyze
- `--json`: Output results as JSON

## Transformation Examples

### Angular Component → React Component

**Angular:**
```typescript
@Component({
  selector: 'app-user',
  template: `
    <div *ngIf="user">
      <h2>{{user.name}}</h2>
      <button (click)="onEdit()">Edit</button>
    </div>
  `
})
export class UserComponent implements OnInit {
  @Input() userId: string;
  @Output() userSelected = new EventEmitter<string>();
  
  user: User | null = null;
  
  constructor(private userService: UserService) {}
  
  ngOnInit() {
    this.loadUser();
  }
  
  async loadUser() {
    this.user = await this.userService.getUser(this.userId);
  }
  
  onEdit() {
    this.userSelected.emit(this.userId);
  }
}
```

**React:**
```typescript
import React, { useState, useEffect } from 'react';
import { useUserService } from './UserService';

interface UserProps {
  userId: string;
  onUserSelected: (userId: string) => void;
}

function User({ userId, onUserSelected }: UserProps) {
  const [user, setUser] = useState<User | null>(null);
  const userService = useUserService();

  useEffect(() => {
    loadUser();
  }, []);

  const loadUser = async () => {
    const userData = await userService.getUser(userId);
    setUser(userData);
  };

  const onEdit = () => {
    onUserSelected(userId);
  };

  return (
    <div>
      {user && (
        <div>
          <h2>{user.name}</h2>
          <button onClick={onEdit}>Edit</button>
        </div>
      )}
    </div>
  );
}

export default User;
```

### Angular Service → React Hook

**Angular:**
```typescript
@Injectable({
  providedIn: 'root'
})
export class UserService {
  constructor(private http: HttpClient) {}
  
  async getUser(id: string): Promise<User> {
    return this.http.get<User>(`/api/users/${id}`).toPromise();
  }
}
```

**React:**
```typescript
import { useState, useCallback } from 'react';
import axios from 'axios';

export const useUserService = () => {
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState(null);

  const getUser = useCallback(async (id: string) => {
    setLoading(true);
    setError(null);
    
    try {
      const response = await axios.get(`/api/users/${id}`);
      return response.data;
    } catch (err) {
      setError(err.message);
      throw err;
    } finally {
      setLoading(false);
    }
  }, []);

  return {
    loading,
    error,
    getUser
  };
};
```

### Angular Pipe → React Hook

**Angular:**
```typescript
@Pipe({ name: 'truncate' })
export class TruncatePipe implements PipeTransform {
  transform(value: string, limit = 25): string {
    return value.length > limit ? value.substr(0, limit) + '...' : value;
  }
}
```

**React:**
```typescript
import { useMemo } from 'react';

export const truncatePipe = (value: string, limit = 25) => {
  return value.length > limit ? value.substr(0, limit) + '...' : value;
};

export const useTruncatePipe = (value: string, limit = 25) => {
  return useMemo(() => truncatePipe(value, limit), [value, limit]);
};
```

## Supported Angular Features

### Components
- ✅ Component classes and decorators
- ✅ Input/Output properties
- ✅ Lifecycle hooks (ngOnInit, ngOnDestroy, etc.)
- ✅ Template and templateUrl
- ✅ StyleUrls and styles
- ✅ Event handlers
- ✅ Property binding
- ✅ Two-way data binding

### Templates
- ✅ Interpolation `{{value}}`
- ✅ Property binding `[property]="value"`
- ✅ Event binding `(event)="handler()"`
- ✅ Two-way binding `[(ngModel)]="value"`
- ✅ Structural directives `*ngIf`, `*ngFor`
- ✅ Attribute directives `ngClass`, `ngStyle`
- ✅ Pipes `value | pipe:args`
- ✅ Template reference variables
- ✅ ng-container and ng-template

### Services
- ✅ Injectable services
- ✅ HTTP client usage
- ✅ Dependency injection
- ✅ RxJS observables → React hooks
- ✅ BehaviorSubject → useState/useContext

### Directives
- ✅ Attribute directives → Custom hooks
- ✅ Structural directives → React components
- ✅ Host listeners → Event handlers
- ✅ Input/Output properties

### Pipes
- ✅ Pure and impure pipes
- ✅ Built-in pipes (date, currency, etc.)
- ✅ Custom pipes → Utility functions
- ✅ Pipe chaining

### Routing
- ✅ Route configuration
- ✅ Router outlets → React Router
- ✅ Route parameters
- ✅ Guards → Route protection
- ✅ Lazy loading → React.lazy

### Modules
- ✅ NgModule → Context providers
- ✅ Feature modules → Component organization
- ✅ Shared modules → Utility exports
- ✅ Routing modules → Route configuration

## Generated Project Structure

```
react-output/
├── src/
│   ├── components/          # Converted components
│   ├── hooks/              # Custom hooks from services
│   ├── services/           # HTTP clients and utilities
│   ├── utils/              # Pipe functions and helpers
│   ├── contexts/           # Context providers from modules
│   ├── pages/              # Route components
│   ├── types/              # TypeScript type definitions
│   ├── styles/             # CSS files
│   └── App.tsx             # Main app component
├── public/
│   └── index.html          # HTML template
├── tests/                  # Test files
├── package.json            # Dependencies and scripts
├── tsconfig.json           # TypeScript configuration
├── vite.config.ts          # Build configuration
└── jest.config.js          # Test configuration
```

## Configuration

Create a `.ng2reactrc.json` file in your project root:

```json
{
  "sourceDir": "./src",
  "outputDir": "./react-output",
  "useTypeScript": true,
  "generateTests": true,
  "preserveStructure": true,
  "reactVersion": "18",
  "targetBrowser": "modern",
  "cssFramework": "none",
  "stateManagement": "context",
  "routingLibrary": "react-router"
}
```

## Testing

The codemod generates comprehensive test files using Jest and React Testing Library:

```bash
# Run generated tests
cd react-output
npm test

# Run with coverage
npm run test -- --coverage
```

## Limitations

- Complex RxJS operators may need manual review
- Custom Angular decorators require manual conversion
- Some Angular-specific patterns may not have direct React equivalents
- Third-party Angular libraries need manual replacement

## Contributing

1. Fork the repository
2. Create a feature branch
3. Add tests for new functionality
4. Submit a pull request

## License

MIT License - see LICENSE file for details.


**Note**: This codemod provides a solid foundation for migration but manual review and testing of the generated code is recommended for production applications.
# codmod-angular-to-react-convert-project
# codmod-angular-to-react-convert-project
