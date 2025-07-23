# React Native + Expo Tech Stack Training Documentation

This comprehensive training guide covers all the technologies used in this mobile application project. Follow the resources and tutorials below to master each component of our tech stack.

## 📱 Core Framework: React Native + Expo

### Overview

- **React Native**: Cross-platform mobile development framework using JavaScript and React
- **Expo**: Platform and toolchain that simplifies React Native development
- **Expo Router**: File-based routing system for universal React Native apps

### 🎥 Recommended Learning Path

#### Beginner (Free YouTube Courses)

**1. JavaScript Mastery - React Native Course for Beginners 2025**

- **Duration**: 3 hours
- **Content**: Build a full-stack movie application with Expo
- **Covers**: Fundamentals, custom styling, routing, API integration, custom hooks, search functionality
- **Link**: Search "React Native Course for Beginners 2025 JavaScript Mastery" on YouTube

**2. notJust․dev - Food Ordering App**

- **Duration**: 8 hours
- **Content**: Build full-stack food ordering mobile app
- **Covers**: React Native, Expo, Expo Router, Supabase, TypeScript, Stripe payments
- **Link**: Search "React Native Food Ordering App notJust.dev" on YouTube

**3. Simon Grimm's YouTube Channel**

- **Projects**: ChatGPT Clone, FinTech Clone (Revolut-inspired)
- **Covers**: Expo Router, API routes, Zustand, TanStack Query, FaceID, Charts, Clerk auth
- **Link**: Search "Simon Grimm React Native" on YouTube

#### Official Documentation

- **Expo Tutorial**: https://docs.expo.dev/tutorial/introduction/
- **Duration**: 2 hours, 9 chapters
- **Content**: Build universal app for Android, iOS, and web

## 🗂️ File-Based Routing: Expo Router

### Overview

Expo Router provides file-based routing similar to Next.js, built on React Navigation.

### Key Concepts

- `app/index.tsx` - Initial route (home page)
- `app/_layout.tsx` - Root layout wrapper
- `app/profile/friends.tsx` - Nested route: `/profile/friends`
- `(tabs)` - Route groups for tab navigation
- `(aux)` - Auxiliary screens outside main navigation

### 📚 Learning Resources

**Official Documentation**

- **Expo Router Introduction**: https://docs.expo.dev/router/introduction/
- **File-based Routing Guide**: https://docs.expo.dev/develop/file-based-routing/
- **Core Concepts**: https://docs.expo.dev/router/basics/core-concepts/

**Tutorial Articles**

- **LogRocket Guide**: [Expo Router adoption guide: Overview, examples, and alternatives](https://blog.logrocket.com/expo-router-adoption-guide/)
- **This Dot Labs**: [File Based Routing with Expo Router](https://www.thisdot.co/blog/file-based-routing-with-expo-router)
- **Galaxies.dev**: [React Native File Based Routing with Expo Router](https://galaxies.dev/react-native-file-based-routing)

## 📘 TypeScript

### Overview

TypeScript provides static type checking for JavaScript, essential for large-scale React Native applications.

### 🎯 Learning Resources

**Official Documentation**

- **React Native TypeScript**: https://reactnative.dev/docs/typescript
- **GitHub Cheatsheet**: https://github.com/typescript-cheatsheets/react

**Best Practices Articles**

- **DEV Community**: [Using TypeScript with React Native: Best Practices](https://dev.to/aneeqakhan/using-typescript-with-react-native-best-practices-62)
- **Medium**: [Building React Native Apps with TypeScript: Best Practices](https://medium.com/@codenova/building-react-native-apps-with-typescript-best-practices-7c5840d45ed8)

### Key Best Practices

- Use strict mode configuration
- Define component props with interfaces
- Leverage utility types (Partial, Pick, Omit, Record)
- Implement proper testing with type safety
- Use ESLint with TypeScript parser

## 🧩 State Management: Jotai

### Overview

Jotai is a primitive and flexible state management library that builds state by combining atoms with automatic render optimization.

### Why Jotai?

- Atomic state management with fine-grained reactivity
- No boilerplate compared to Redux
- Automatic optimization of re-renders
- Perfect for complex state interdependencies

### 📖 Learning Resources

**Official Documentation**

- **Jotai Official Site**: https://jotai.org
- **GitHub Repository**: https://github.com/pmndrs/jotai

**Tutorial Articles**

- **DEV Community**: [State Management in 2025: When to Use Context, Redux, Zustand, or Jotai](https://dev.to/hijazi313/state-management-in-2025-when-to-use-context-redux-zustand-or-jotai-2d2k)
- **The New Stack**: [How to Simplify Global State Management in React Using Jotai](https://thenewstack.io/how-to-simplify-global-state-management-in-react-using-jotai/)
- **Medium**: [Mastering State Management in React Native Apps in 2025](https://medium.com/@sharmapraveen91/mastering-state-management-in-react-native-apps-in-2025-a-comprehensive-guide-5399b6693dc1)

### Core Concepts

```typescript
// Basic atom
const countAtom = atom(0)

// Using atoms in components
const Counter = () => {
  const [count, setCount] = useAtom(countAtom)
  return (
    <View>
      <Text>{count}</Text>
      <Button onPress={() => setCount(c => c + 1)} title="+" />
    </View>
  )
}
```

### Best Practices

- Use `useAtomValue` and `useSetAtom` separately to optimize re-renders
- Implement derived atoms for complex state logic
- Keep atoms small and focused
- Use atoms for global state, useState for local component state

## 🔄 Server State: TanStack Query (React Query)

### Overview

TanStack Query handles server state management, caching, synchronization, and data fetching with zero configuration.

### Key Benefits

- Automatic caching and background updates
- Request deduplication
- Optimistic updates
- Offline support
- No global state management needed

### 🎓 Learning Resources

**Official Documentation**

- **TanStack Query**: https://tanstack.com/query/latest
- **React Docs**: https://tanstack.com/query/latest/docs/framework/react/overview
- **Quick Start**: https://tanstack.com/query/latest/docs/framework/react/quick-start

**Comprehensive Guides**

- **Zero to Mastery**: "Beginner's Guide to React Query (Now Tanstack Query)"
- **DEV Community**: "Mastering TanStack Query: A Comprehensive Guide"
- **ThapaTechnical**: "TanStack React Query in One Video + Resources"

### Basic Usage

```typescript
import { useQuery } from '@tanstack/react-query'

function Profile() {
  const { data, isLoading, error } = useQuery({
    queryKey: ['profile'],
    queryFn: () => fetch('/api/profile').then(res => res.json())
  })

  if (isLoading) return <Text>Loading...</Text>
  if (error) return <Text>Error: {error.message}</Text>
  return <Text>Hello {data.name}!</Text>
}
```

## 🎨 Styling: NativeWind (Tailwind CSS)

### Overview

NativeWind brings the utility-first workflow of Tailwind CSS to React Native applications with consistent cross-platform styling.

### Benefits

- Familiar Tailwind CSS classes
- Consistent design language
- Mobile-first responsive design
- No separate stylesheets needed

### 📝 Learning Resources

**Official Documentation**

- **NativeWind Official**: https://www.nativewind.dev/
- **Installation Guide**: https://www.nativewind.dev/docs/getting-started/installation
- **GitHub Repository**: https://github.com/nativewind/nativewind

**Recent Tutorials (2024-2025)**

- **LogRocket**: [Getting started with NativeWind: Tailwind for React Native](https://blog.logrocket.com/getting-started-nativewind-tailwind-react-native/)
- **DEV Community**: [Getting Started with NativeWind: Using Tailwind CSS in React Native](https://dev.to/gamertense/getting-started-with-nativewind-using-tailwind-css-in-react-native-13e6)

### Basic Usage

```typescript
import { View, Text } from 'react-native'

function Card() {
  return (
    <View className="bg-white p-4 rounded-lg shadow-lg">
      <Text className="text-xl font-bold text-gray-800">Hello World</Text>
    </View>
  )
}
```

## 🛠️ Development Workflow

### Required Tools

- Node.js (latest LTS)
- Yarn 4.9.2
- Expo CLI
- Xcode (for iOS development)
- Android Studio (for Android development)

### Development Commands

```bash
# Start development server
yarn start

# Platform-specific development
yarn ios                  # Run on iOS
yarn android              # Run on Android

# Build commands
yarn eas-build:android     # Android development build
yarn eas-build:ios         # iOS development build

# Code quality
npx eslint .              # Linting
npx tsc --noEmit          # Type checking
npx prettier --write .    # Code formatting
```

### Project Structure

```
app/                      # File-based routes
├── _layout.tsx          # Root layout
├── index.tsx            # Home page
├── (tabs)/              # Tab navigation
├── (aux)/               # Auxiliary screens
└── auth/                # Authentication screens

components/shared/        # Reusable UI components
store/                   # Jotai atoms and state
services/                # API integration
hooks/                   # Custom React hooks
layout/                  # Layout components
```

## 🚀 Getting Started

1. **Clone and Setup**

    ```bash
    git clone <repository-url>
    cd sos-mobile-v2
    yarn install
    ```

2. **Environment Configuration**

    ```bash
    cp example.env.sample .env
    # Configure environment variables
    ```

3. **Start Development**

    ```bash
    yarn start
    ```

4. **Begin Learning**
    - Start with JavaScript Mastery's React Native crash course
    - Set up your development environment
    - Explore the existing codebase alongside the tutorials

## 📚 Additional Resources

### Official Documentation

- [React Native](https://reactnative.dev/)
- [Expo](https://docs.expo.dev/)
- [TypeScript](https://www.typescriptlang.org/)
- [Jotai](https://jotai.org/)
- [TanStack Query](https://tanstack.com/query/latest)
- [NativeWind](https://www.nativewind.dev/)

### Community Resources

- [React Native Directory](https://reactnative.directory/)
- [Expo Community](https://forums.expo.dev/)
- [React Native Community](https://github.com/react-native-community)

### YouTube Channels to Follow

- JavaScript Mastery
- notJust․dev
- Simon Grimm
- William Candillon (React Native Animations)
- Catalin Miron (React Native UI)

This training documentation provides a comprehensive roadmap for mastering our tech stack. Start with the recommended learning path and reference the specific documentation as needed during development.
