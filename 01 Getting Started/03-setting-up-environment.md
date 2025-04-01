# Setting Up Your Development Environment

## Introduction

Setting up a proper development environment is crucial for a smooth React with TypeScript development experience. In this section, we'll cover everything you need to get started, from essential tools to recommended configurations.

## Required Tools

### 1. Node.js and npm

First, ensure you have Node.js installed (which includes npm):

```bash
# Check Node.js version
node --version  # Should be 14.x or higher

# Check npm version
npm --version   # Should be 6.x or higher
```

### 2. Code Editor

While you can use any code editor, we strongly recommend Visual Studio Code for React development. It provides excellent TypeScript support and React-specific extensions.

#### Recommended VS Code Extensions:
- ESLint
- Prettier
- TypeScript and JavaScript Language Features
- React Developer Tools
- GitLens
- Error Lens

### 3. Browser Extensions

Install these browser extensions for better development experience:
- React Developer Tools (Chrome/Firefox)
- Redux DevTools (if using Redux)

## Project Setup Options

### 1. Create React App with TypeScript

The simplest way to start a new React TypeScript project:

```bash
# Create a new project
npx create-react-app my-app --template typescript

# Navigate to project directory
cd my-app

# Start development server
npm start
```

### 2. Vite with React and TypeScript

Vite is a modern build tool that offers faster development experience:

```bash
# Create a new Vite project
npm create vite@latest my-app -- --template react-ts

# Navigate to project directory
cd my-app

# Install dependencies
npm install

# Start development server
npm run dev
```

### 3. Next.js with TypeScript

For full-stack applications or static site generation:

```bash
# Create a new Next.js project
npx create-next-app@latest my-app --typescript

# Navigate to project directory
cd my-app

# Start development server
npm run dev
```

## Essential Configuration Files

### 1. TypeScript Configuration (tsconfig.json)

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
    "jsx": "react-jsx",
    "baseUrl": "src"
  },
  "include": ["src"],
  "exclude": ["node_modules"]
}
```

### 2. ESLint Configuration (.eslintrc.json)

```json
{
  "extends": [
    "eslint:recommended",
    "plugin:@typescript-eslint/recommended",
    "plugin:react/recommended",
    "plugin:react-hooks/recommended"
  ],
  "parser": "@typescript-eslint/parser",
  "plugins": ["@typescript-eslint", "react", "react-hooks"],
  "rules": {
    "react/react-in-jsx-scope": "off",
    "react/prop-types": "off",
    "@typescript-eslint/explicit-module-boundary-types": "off"
  },
  "settings": {
    "react": {
      "version": "detect"
    }
  }
}
```

### 3. Prettier Configuration (.prettierrc)

```json
{
  "semi": true,
  "trailingComma": "es5",
  "singleQuote": true,
  "printWidth": 100,
  "tabWidth": 2,
  "useTabs": false
}
```

## Essential Dependencies

### 1. Core Dependencies

```bash
# Install core dependencies
npm install react react-dom @types/react @types/react-dom typescript

# Install development dependencies
npm install --save-dev @typescript-eslint/parser @typescript-eslint/eslint-plugin
npm install --save-dev eslint-plugin-react eslint-plugin-react-hooks
npm install --save-dev prettier eslint-config-prettier eslint-plugin-prettier
```

### 2. Optional but Recommended Dependencies

```bash
# Routing
npm install react-router-dom @types/react-router-dom

# State Management
npm install @reduxjs/toolkit react-redux @types/react-redux

# Form Handling
npm install react-hook-form

# Styling
npm install styled-components @types/styled-components
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

## VS Code Settings

Create or update `.vscode/settings.json`:

```json
{
  "editor.formatOnSave": true,
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true
  },
  "typescript.tsdk": "node_modules/typescript/lib",
  "typescript.enablePromptUseWorkspaceTsdk": true,
  "editor.defaultFormatter": "esbenp.prettier-vscode"
}
```

## Practice Exercises

1. Set up a new React TypeScript project using Vite:
   - Configure ESLint and Prettier
   - Set up VS Code with recommended extensions
   - Create a basic component structure

2. Configure a custom path alias in tsconfig.json:
   - Set up `@components` and `@utils` path aliases
   - Create example components using the aliases
   - Verify the configuration works

3. Set up a development environment with:
   - Hot Module Replacement (HMR)
   - Source maps
   - Error boundaries
   - Development tools

## Key Takeaways

- A well-configured development environment is essential for productivity
- VS Code with appropriate extensions provides the best development experience
- TypeScript configuration should be strict but practical
- ESLint and Prettier help maintain code quality
- Proper project structure and organization is important from the start

## Next Steps

In the next section, we'll create your first React TypeScript project and explore the project structure in detail. 