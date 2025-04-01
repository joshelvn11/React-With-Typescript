# 1.3 Setting Up Your Development Environment

## Prerequisites

Before setting up your React + TypeScript development environment, ensure you have the following installed:

1. **Node.js and npm**
   - Download from [nodejs.org](https://nodejs.org/)
   - Recommended version: LTS (Long Term Support)
   - Verify installation:
     ```bash
     node --version
     npm --version
     ```

2. **Git**
   - Download from [git-scm.com](https://git-scm.com/)
   - Required for version control and package management

## IDE Setup

### Visual Studio Code (Recommended)

1. **Install VS Code**
   - Download from [code.visualstudio.com](https://code.visualstudio.com/)
   - Install recommended extensions:
     - ESLint
     - Prettier
     - TypeScript and JavaScript Language Features
     - React Developer Tools

2. **Configure VS Code Settings**
   ```json
   {
     "editor.formatOnSave": true,
     "editor.defaultFormatter": "esbenp.prettier-vscode",
     "editor.codeActionsOnSave": {
       "source.fixAll.eslint": true
     },
     "typescript.tsdk": "node_modules/typescript/lib"
   }
   ```

3. **Install Essential Extensions**
   - ESLint: `dbaeumer.vscode-eslint`
   - Prettier: `esbenp.prettier-vscode`
   - React Developer Tools: `dsznajder.es7-react-js-snippets`
   - TypeScript Importer: `pmneo.tsimporter`
   - Path Intellisense: `christian-kohler.path-intellisense`

## TypeScript Configuration

1. **Install TypeScript Globally**
   ```bash
   npm install -g typescript
   ```

2. **Create TypeScript Configuration**
   ```bash
   tsc --init
   ```

3. **Configure tsconfig.json**
   ```json
   {
     "compilerOptions": {
       "target": "es5",
       "lib": ["dom", "dom.iterable", "esnext"],
       "allowJs": true,
       "skipLibCheck": true,
       "esModuleInterop": true,
       "allowSyntheticDefaultImports": true,
       "strict": true,
       "forceConsistentCasingInFileNames": true,
       "noFallthroughCasesInSwitch": true,
       "module": "esnext",
       "moduleResolution": "node",
       "resolveJsonModule": true,
       "isolatedModules": true,
       "noEmit": true,
       "jsx": "react-jsx"
     },
     "include": ["src"]
   }
   ```

## Development Tools

### 1. ESLint Setup

1. **Install ESLint and React Plugin**
   ```bash
   npm install --save-dev eslint @typescript-eslint/parser @typescript-eslint/eslint-plugin eslint-plugin-react eslint-plugin-react-hooks
   ```

2. **Configure .eslintrc.js**
   ```javascript
   module.exports = {
     parser: '@typescript-eslint/parser',
     extends: [
       'plugin:react/recommended',
       'plugin:@typescript-eslint/recommended',
       'plugin:react-hooks/recommended',
     ],
     parserOptions: {
       ecmaVersion: 2020,
       sourceType: 'module',
       ecmaFeatures: {
         jsx: true,
       },
     },
     rules: {
       // Add custom rules here
     },
     settings: {
       react: {
         version: 'detect',
       },
     },
   };
   ```

### 2. Prettier Setup

1. **Install Prettier**
   ```bash
   npm install --save-dev prettier
   ```

2. **Configure .prettierrc**
   ```json
   {
     "semi": true,
     "trailingComma": "es5",
     "singleQuote": true,
     "printWidth": 100,
     "tabWidth": 2
   }
   ```

### 3. Git Configuration

1. **Initialize Git Repository**
   ```bash
   git init
   ```

2. **Create .gitignore**
   ```text
   # dependencies
   /node_modules
   /.pnp
   .pnp.js

   # testing
   /coverage

   # production
   /build

   # misc
   .DS_Store
   .env.local
   .env.development.local
   .env.test.local
   .env.production.local

   npm-debug.log*
   yarn-debug.log*
   yarn-error.log*
   ```

## Project Structure

Create a basic project structure:

```bash
my-react-app/
├── src/
│   ├── components/
│   ├── pages/
│   ├── hooks/
│   ├── utils/
│   ├── types/
│   ├── assets/
│   ├── App.tsx
│   └── index.tsx
├── public/
├── package.json
├── tsconfig.json
├── .eslintrc.js
├── .prettierrc
└── .gitignore
```

## Development Scripts

Add these scripts to your package.json:

```json
{
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test",
    "eject": "react-scripts eject",
    "lint": "eslint src --ext .ts,.tsx",
    "lint:fix": "eslint src --ext .ts,.tsx --fix",
    "format": "prettier --write \"src/**/*.{ts,tsx}\""
  }
}
```

## Browser Extensions

1. **React Developer Tools**
   - Install from [Chrome Web Store](https://chrome.google.com/webstore/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi)
   - Helps debug React applications
   - Inspect component hierarchy
   - Monitor state changes

2. **TypeScript Playground**
   - Install from [Chrome Web Store](https://chrome.google.com/webstore/detail/typescript-playground/ipdghkfpnkfcnkfhbkfcckfbfhdkjfghf)
   - Test TypeScript code
   - Learn TypeScript features

## Practice Exercise

Set up a new React + TypeScript project:

1. **Create a new project**
   ```bash
   npx create-react-app my-app --template typescript
   cd my-app
   ```

2. **Install additional dependencies**
   ```bash
   npm install --save-dev eslint @typescript-eslint/parser @typescript-eslint/eslint-plugin eslint-plugin-react eslint-plugin-react-hooks prettier
   ```

3. **Configure the development environment**
   - Copy the configuration files provided above
   - Install recommended VS Code extensions
   - Set up ESLint and Prettier

4. **Create a basic component**
   ```typescript
   // src/components/Hello.tsx
   import React from 'react';

   interface HelloProps {
     name: string;
   }

   const Hello: React.FC<HelloProps> = ({ name }) => {
     return <h1>Hello, {name}!</h1>;
   };

   export default Hello;
   ```

## Key Takeaways

- Proper development environment setup is crucial for efficient development
- TypeScript configuration is essential for type checking
- ESLint and Prettier help maintain code quality
- Browser extensions enhance development experience
- Project structure should be organized and scalable

## Common Pitfalls to Avoid

1. **Incorrect TypeScript Configuration**
   - Double-check compiler options
   - Ensure proper module resolution
   - Verify JSX support

2. **Missing Development Tools**
   - Install all recommended extensions
   - Configure ESLint and Prettier properly
   - Set up proper Git configuration

3. **Poor Project Structure**
   - Plan your folder structure
   - Follow consistent naming conventions
   - Organize code logically

## Further Reading

- [VS Code Documentation](https://code.visualstudio.com/docs)
- [TypeScript Configuration Guide](https://www.typescriptlang.org/docs/handbook/tsconfig-json.html)
- [ESLint Configuration Guide](https://eslint.org/docs/user-guide/configuring)
- [Prettier Configuration Guide](https://prettier.io/docs/en/configuration.html) 