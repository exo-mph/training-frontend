# React Native + Expo Tech Stack Training Exercises

## 🎯 Project Overview

Build a **Personal Task Manager App** using the complete tech stack covered in the training documentation. This hands-on project will progressively introduce you to React Native, Expo Router, TypeScript, Jotai, TanStack Query, and NativeWind through practical exercises.

### Final App Features
- Task management with categories
- User authentication
- Real-time data synchronization
- Dark/light theme switching
- Profile management
- Push notifications

---

## 📋 Prerequisites

Before starting, ensure you have:
- ✅ Completed the [JavaScript Mastery React Native crash course](https://www.youtube.com/results?search_query=React+Native+Course+for+Beginners+2025+JavaScript+Mastery)
- ✅ Read the [Expo Router introduction](https://docs.expo.dev/router/introduction/)
- ✅ Basic understanding of React and JavaScript
- ✅ Development environment set up (Node.js, Expo CLI, Yarn)

---

## 🚀 Exercise 1: Project Setup & Basic Navigation

### Goal
Set up a new Expo project with TypeScript and implement basic file-based routing.

### Tasks

#### 1.1 Create New Project
```bash
npx create-expo-app TaskManagerApp --template blank-typescript
cd TaskManagerApp
```

#### 1.2 Install Required Dependencies
```bash
npx expo install expo-router react-native-safe-area-context react-native-screens expo-linking expo-constants expo-status-bar
```

#### 1.3 Configure Expo Router
Update `app.json`:
```json
{
  "expo": {
    "scheme": "taskmanager",
    "web": {
      "bundler": "metro"
    }
  }
}
```

#### 1.4 Create File-Based Routes
Create the following file structure:
```
app/
├── _layout.tsx          # Root layout
├── index.tsx           # Home screen
├── (tabs)/
│   ├── _layout.tsx     # Tab layout
│   ├── tasks.tsx       # Tasks screen
│   ├── profile.tsx     # Profile screen
│   └── settings.tsx    # Settings screen
└── (auth)/
    ├── login.tsx       # Login screen
    └── register.tsx    # Register screen
```

#### 1.5 Implement Root Layout
Create `app/_layout.tsx`:
```tsx
import { Stack } from 'expo-router';
import { StatusBar } from 'expo-status-bar';

export default function RootLayout() {
  return (
    <>
      <Stack>
        <Stack.Screen name="(tabs)" options={{ headerShown: false }} />
        <Stack.Screen name="(auth)" options={{ headerShown: false }} />
      </Stack>
      <StatusBar style="auto" />
    </>
  );
}
```

### ✅ Checkpoint 1
- [ ] App runs without errors
- [ ] Navigation between screens works
- [ ] File-based routing structure is correct
- [ ] TypeScript compilation succeeds

**Resources:**
- [Expo Router File-Based Routing](https://docs.expo.dev/develop/file-based-routing/)
- [This Dot Labs: File Based Routing with Expo Router](https://www.thisdot.co/blog/file-based-routing-with-expo-router)

---

## 🎨 Exercise 2: Styling with NativeWind

### Goal
Set up NativeWind and create a consistent design system with dark/light theme support.

### Tasks

#### 2.1 Install NativeWind
```bash
npm install nativewind tailwindcss@^3.4.17
npx tailwindcss init
```

#### 2.2 Configure Tailwind
Update `tailwind.config.js`:
```javascript
module.exports = {
  content: ["./app/**/*.{js,jsx,ts,tsx}"],
  presets: [require("nativewind/preset")],
  theme: {
    extend: {
      colors: {
        primary: {
          50: '#eff6ff',
          500: '#3b82f6',
          600: '#2563eb',
          900: '#1e3a8a',
        }
      }
    },
  },
  plugins: [],
}
```

#### 2.3 Update Metro Config
Create/update `metro.config.js`:
```javascript
const { getDefaultConfig } = require("expo/metro-config");
const { withNativeWind } = require('nativewind/metro');

const config = getDefaultConfig(__dirname)

module.exports = withNativeWind(config, { input: './global.css' })
```

#### 2.4 Create Global Styles
Create `global.css`:
```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

#### 2.5 Style Home Screen
Update `app/index.tsx`:
```tsx
import { View, Text, Pressable } from 'react-native';
import { Link } from 'expo-router';
import '../global.css';

export default function HomeScreen() {
  return (
    <View className="flex-1 bg-gray-50 justify-center items-center p-6">
      <View className="bg-white rounded-xl p-8 shadow-lg w-full max-w-sm">
        <Text className="text-3xl font-bold text-gray-900 text-center mb-2">
          Task Manager
        </Text>
        <Text className="text-gray-600 text-center mb-8">
          Organize your life, one task at a time
        </Text>
        
        <Link href="/(tabs)/tasks" asChild>
          <Pressable className="bg-primary-500 rounded-lg py-4 mb-4">
            <Text className="text-white text-center font-semibold text-lg">
              Get Started
            </Text>
          </Pressable>
        </Link>
        
        <Link href="/(auth)/login" asChild>
          <Pressable className="border border-primary-500 rounded-lg py-4">
            <Text className="text-primary-500 text-center font-semibold text-lg">
              Sign In
            </Text>
          </Pressable>
        </Link>
      </View>
    </View>
  );
}
```

### ✅ Checkpoint 2
- [ ] NativeWind classes render correctly
- [ ] Design is consistent and professional
- [ ] Colors from custom theme work
- [ ] Responsive layout on different screen sizes

**Resources:**
- [NativeWind Official Documentation](https://www.nativewind.dev/)
- [LogRocket: Getting started with NativeWind](https://blog.logrocket.com/getting-started-nativewind-tailwind-react-native/)

---

## 🧩 Exercise 3: State Management with Jotai

### Goal
Implement global state management using Jotai atoms for user authentication and task management.

### Tasks

#### 3.1 Install Jotai
```bash
npm install jotai
```

#### 3.2 Create User Auth Atoms
Create `store/auth.ts`:
```tsx
import { atom } from 'jotai';

export interface User {
  id: string;
  name: string;
  email: string;
  avatar?: string;
}

export const userAtom = atom<User | null>(null);
export const isAuthenticatedAtom = atom((get) => get(userAtom) !== null);
export const isLoadingAuthAtom = atom(false);

// Auth actions
export const loginAtom = atom(
  null,
  (get, set, user: User) => {
    set(userAtom, user);
    set(isLoadingAuthAtom, false);
  }
);

export const logoutAtom = atom(
  null,
  (get, set) => {
    set(userAtom, null);
  }
);
```

#### 3.3 Create Task Management Atoms
Create `store/tasks.ts`:
```tsx
import { atom } from 'jotai';

export interface Task {
  id: string;
  title: string;
  description?: string;
  completed: boolean;
  category: 'work' | 'personal' | 'shopping' | 'health';
  priority: 'low' | 'medium' | 'high';
  createdAt: Date;
  dueDate?: Date;
}

export const tasksAtom = atom<Task[]>([]);
export const selectedCategoryAtom = atom<string>('all');

// Derived atoms
export const completedTasksAtom = atom((get) => 
  get(tasksAtom).filter(task => task.completed)
);

export const pendingTasksAtom = atom((get) => 
  get(tasksAtom).filter(task => !task.completed)
);

export const filteredTasksAtom = atom((get) => {
  const tasks = get(tasksAtom);
  const category = get(selectedCategoryAtom);
  
  if (category === 'all') return tasks;
  return tasks.filter(task => task.category === category);
});

// Task actions
export const addTaskAtom = atom(
  null,
  (get, set, newTask: Omit<Task, 'id' | 'createdAt'>) => {
    const tasks = get(tasksAtom);
    const task: Task = {
      ...newTask,
      id: Date.now().toString(),
      createdAt: new Date(),
    };
    set(tasksAtom, [...tasks, task]);
  }
);

export const toggleTaskAtom = atom(
  null,
  (get, set, taskId: string) => {
    const tasks = get(tasksAtom);
    const updatedTasks = tasks.map(task =>
      task.id === taskId ? { ...task, completed: !task.completed } : task
    );
    set(tasksAtom, updatedTasks);
  }
);

export const deleteTaskAtom = atom(
  null,
  (get, set, taskId: string) => {
    const tasks = get(tasksAtom);
    set(tasksAtom, tasks.filter(task => task.id !== taskId));
  }
);
```

#### 3.4 Create Theme Atoms
Create `store/theme.ts`:
```tsx
import { atom } from 'jotai';

export type Theme = 'light' | 'dark' | 'system';

export const themeAtom = atom<Theme>('system');
export const isDarkModeAtom = atom((get) => {
  const theme = get(themeAtom);
  if (theme === 'system') {
    // In a real app, you'd check system preference
    return false;
  }
  return theme === 'dark';
});
```

#### 3.5 Implement Tasks Screen with State
Update `app/(tabs)/tasks.tsx`:
```tsx
import { View, Text, FlatList, Pressable } from 'react-native';
import { useAtom, useAtomValue, useSetAtom } from 'jotai';
import { 
  filteredTasksAtom, 
  selectedCategoryAtom, 
  toggleTaskAtom,
  addTaskAtom,
  Task 
} from '../../store/tasks';

const categories = [
  { id: 'all', label: 'All', color: 'bg-gray-500' },
  { id: 'work', label: 'Work', color: 'bg-blue-500' },
  { id: 'personal', label: 'Personal', color: 'bg-green-500' },
  { id: 'shopping', label: 'Shopping', color: 'bg-yellow-500' },
  { id: 'health', label: 'Health', color: 'bg-red-500' },
];

export default function TasksScreen() {
  const tasks = useAtomValue(filteredTasksAtom);
  const [selectedCategory, setSelectedCategory] = useAtom(selectedCategoryAtom);
  const toggleTask = useSetAtom(toggleTaskAtom);
  const addTask = useSetAtom(addTaskAtom);

  const addSampleTask = () => {
    addTask({
      title: 'Sample Task',
      description: 'This is a sample task',
      completed: false,
      category: 'work',
      priority: 'medium',
    });
  };

  const renderTask = ({ item }: { item: Task }) => (
    <View className="bg-white p-4 mb-3 rounded-lg shadow-sm border border-gray-100">
      <View className="flex-row items-center justify-between">
        <View className="flex-1">
          <Text className={`text-lg font-semibold ${
            item.completed ? 'text-gray-500 line-through' : 'text-gray-900'
          }`}>
            {item.title}
          </Text>
          {item.description && (
            <Text className="text-gray-600 mt-1">{item.description}</Text>
          )}
          <View className="flex-row items-center mt-2">
            <View className={`px-2 py-1 rounded-full mr-2 ${
              categories.find(c => c.id === item.category)?.color || 'bg-gray-500'
            }`}>
              <Text className="text-white text-xs font-medium">
                {categories.find(c => c.id === item.category)?.label}
              </Text>
            </View>
            <View className={`px-2 py-1 rounded-full ${
              item.priority === 'high' ? 'bg-red-100' : 
              item.priority === 'medium' ? 'bg-yellow-100' : 'bg-green-100'
            }`}>
              <Text className={`text-xs font-medium ${
                item.priority === 'high' ? 'text-red-800' : 
                item.priority === 'medium' ? 'text-yellow-800' : 'text-green-800'
              }`}>
                {item.priority}
              </Text>
            </View>
          </View>
        </View>
        <Pressable
          onPress={() => toggleTask(item.id)}
          className={`w-6 h-6 rounded-full border-2 ${
            item.completed 
              ? 'bg-green-500 border-green-500' 
              : 'border-gray-300'
          }`}
        >
          {item.completed && (
            <Text className="text-white text-center text-xs leading-5">✓</Text>
          )}
        </Pressable>
      </View>
    </View>
  );

  return (
    <View className="flex-1 bg-gray-50">
      {/* Category Filter */}
      <View className="p-4">
        <Text className="text-xl font-bold text-gray-900 mb-4">My Tasks</Text>
        <FlatList
          horizontal
          showsHorizontalScrollIndicator={false}
          data={categories}
          keyExtractor={(item) => item.id}
          renderItem={({ item }) => (
            <Pressable
              onPress={() => setSelectedCategory(item.id)}
              className={`px-4 py-2 rounded-full mr-3 ${
                selectedCategory === item.id
                  ? 'bg-primary-500'
                  : 'bg-white border border-gray-200'
              }`}
            >
              <Text className={`font-medium ${
                selectedCategory === item.id ? 'text-white' : 'text-gray-700'
              }`}>
                {item.label}
              </Text>
            </Pressable>
          )}
        />
      </View>

      {/* Tasks List */}
      <View className="flex-1 px-4">
        {tasks.length === 0 ? (
          <View className="flex-1 justify-center items-center">
            <Text className="text-gray-500 text-lg mb-4">No tasks yet</Text>
            <Pressable
              onPress={addSampleTask}
              className="bg-primary-500 px-6 py-3 rounded-lg"
            >
              <Text className="text-white font-semibold">Add Sample Task</Text>
            </Pressable>
          </View>
        ) : (
          <FlatList
            data={tasks}
            keyExtractor={(item) => item.id}
            renderItem={renderTask}
            showsVerticalScrollIndicator={false}
          />
        )}
      </View>
    </View>
  );
}
```

### ✅ Checkpoint 3
- [ ] Jotai atoms are working correctly
- [ ] State updates trigger re-renders
- [ ] Task filtering by category works
- [ ] Task completion toggle works
- [ ] Derived atoms calculate correctly

**Resources:**
- [Jotai Official Documentation](https://jotai.org)
- [The New Stack: How to Simplify Global State Management with Jotai](https://thenewstack.io/how-to-simplify-global-state-management-in-react-using-jotai/)

---

## 🔄 Exercise 4: Server State with TanStack Query

### Goal
Implement API integration using TanStack Query for data fetching, caching, and synchronization.

### Tasks

#### 4.1 Install TanStack Query
```bash
npm install @tanstack/react-query
```

#### 4.2 Set Up Query Client
Create `providers/QueryProvider.tsx`:
```tsx
import { QueryClient, QueryClientProvider } from '@tanstack/react-query';
import { ReactNode } from 'react';

const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      staleTime: 1000 * 60 * 5, // 5 minutes
      gcTime: 1000 * 60 * 30, // 30 minutes
    },
  },
});

interface QueryProviderProps {
  children: ReactNode;
}

export default function QueryProvider({ children }: QueryProviderProps) {
  return (
    <QueryClientProvider client={queryClient}>
      {children}
    </QueryClientProvider>
  );
}
```

#### 4.3 Create API Service
Create `services/api.ts`:
```tsx
// Mock API service - replace with real API endpoints
export interface ApiTask {
  id: string;
  title: string;
  description?: string;
  completed: boolean;
  category: 'work' | 'personal' | 'shopping' | 'health';
  priority: 'low' | 'medium' | 'high';
  createdAt: string;
  dueDate?: string;
  userId: string;
}

export interface ApiUser {
  id: string;
  name: string;
  email: string;
  avatar?: string;
}

// Mock data storage
let mockTasks: ApiTask[] = [
  {
    id: '1',
    title: 'Complete project proposal',
    description: 'Write and submit the Q1 project proposal',
    completed: false,
    category: 'work',
    priority: 'high',
    createdAt: new Date().toISOString(),
    userId: 'user1',
  },
  {
    id: '2',
    title: 'Buy groceries',
    description: 'Milk, bread, eggs, fruits',
    completed: false,
    category: 'shopping',
    priority: 'medium',
    createdAt: new Date().toISOString(),
    userId: 'user1',
  },
];

let mockUser: ApiUser = {
  id: 'user1',
  name: 'John Doe',
  email: 'john@example.com',
  avatar: 'https://via.placeholder.com/100',
};

// Simulate network delay
const delay = (ms: number) => new Promise(resolve => setTimeout(resolve, ms));

export const api = {
  // Auth endpoints
  async login(email: string, password: string): Promise<ApiUser> {
    await delay(1000);
    if (email === 'demo@example.com' && password === 'password') {
      return mockUser;
    }
    throw new Error('Invalid credentials');
  },

  async register(name: string, email: string, password: string): Promise<ApiUser> {
    await delay(1000);
    const newUser: ApiUser = {
      id: Date.now().toString(),
      name,
      email,
    };
    mockUser = newUser;
    return newUser;
  },

  // Task endpoints
  async getTasks(userId: string): Promise<ApiTask[]> {
    await delay(500);
    return mockTasks.filter(task => task.userId === userId);
  },

  async createTask(task: Omit<ApiTask, 'id' | 'createdAt'>): Promise<ApiTask> {
    await delay(500);
    const newTask: ApiTask = {
      ...task,
      id: Date.now().toString(),
      createdAt: new Date().toISOString(),
    };
    mockTasks.push(newTask);
    return newTask;
  },

  async updateTask(taskId: string, updates: Partial<ApiTask>): Promise<ApiTask> {
    await delay(500);
    const taskIndex = mockTasks.findIndex(task => task.id === taskId);
    if (taskIndex === -1) throw new Error('Task not found');
    
    mockTasks[taskIndex] = { ...mockTasks[taskIndex], ...updates };
    return mockTasks[taskIndex];
  },

  async deleteTask(taskId: string): Promise<void> {
    await delay(500);
    mockTasks = mockTasks.filter(task => task.id !== taskId);
  },

  // User endpoints
  async getProfile(userId: string): Promise<ApiUser> {
    await delay(500);
    return mockUser;
  },

  async updateProfile(userId: string, updates: Partial<ApiUser>): Promise<ApiUser> {
    await delay(500);
    mockUser = { ...mockUser, ...updates };
    return mockUser;
  },
};
```

#### 4.4 Create Query Hooks
Create `hooks/useTasks.ts`:
```tsx
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';
import { api, ApiTask } from '../services/api';

export const useTasks = (userId: string) => {
  return useQuery({
    queryKey: ['tasks', userId],
    queryFn: () => api.getTasks(userId),
    enabled: !!userId,
  });
};

export const useCreateTask = () => {
  const queryClient = useQueryClient();
  
  return useMutation({
    mutationFn: api.createTask,
    onSuccess: (newTask) => {
      queryClient.setQueryData(['tasks', newTask.userId], (old: ApiTask[] = []) => [
        ...old,
        newTask,
      ]);
    },
  });
};

export const useUpdateTask = () => {
  const queryClient = useQueryClient();
  
  return useMutation({
    mutationFn: ({ taskId, updates }: { taskId: string; updates: Partial<ApiTask> }) =>
      api.updateTask(taskId, updates),
    onSuccess: (updatedTask) => {
      queryClient.setQueryData(['tasks', updatedTask.userId], (old: ApiTask[] = []) =>
        old.map(task => task.id === updatedTask.id ? updatedTask : task)
      );
    },
  });
};

export const useDeleteTask = () => {
  const queryClient = useQueryClient();
  
  return useMutation({
    mutationFn: api.deleteTask,
    onSuccess: (_, taskId) => {
      queryClient.setQueryData(['tasks'], (old: ApiTask[] = []) =>
        old.filter(task => task.id !== taskId)
      );
    },
  });
};
```

#### 4.5 Create Auth Hooks
Create `hooks/useAuth.ts`:
```tsx
import { useMutation, useQuery } from '@tanstack/react-query';
import { useAtom, useSetAtom } from 'jotai';
import { api } from '../services/api';
import { userAtom, isLoadingAuthAtom } from '../store/auth';

export const useLogin = () => {
  const setUser = useSetAtom(userAtom);
  const setIsLoading = useSetAtom(isLoadingAuthAtom);
  
  return useMutation({
    mutationFn: ({ email, password }: { email: string; password: string }) =>
      api.login(email, password),
    onMutate: () => {
      setIsLoading(true);
    },
    onSuccess: (user) => {
      setUser(user);
      setIsLoading(false);
    },
    onError: () => {
      setIsLoading(false);
    },
  });
};

export const useRegister = () => {
  const setUser = useSetAtom(userAtom);
  const setIsLoading = useSetAtom(isLoadingAuthAtom);
  
  return useMutation({
    mutationFn: ({ name, email, password }: { name: string; email: string; password: string }) =>
      api.register(name, email, password),
    onMutate: () => {
      setIsLoading(true);
    },
    onSuccess: (user) => {
      setUser(user);
      setIsLoading(false);
    },
    onError: () => {
      setIsLoading(false);
    },
  });
};

export const useProfile = (userId: string) => {
  return useQuery({
    queryKey: ['profile', userId],
    queryFn: () => api.getProfile(userId),
    enabled: !!userId,
  });
};
```

#### 4.6 Update Root Layout with Providers
Update `app/_layout.tsx`:
```tsx
import { Stack } from 'expo-router';
import { StatusBar } from 'expo-status-bar';
import QueryProvider from '../providers/QueryProvider';

export default function RootLayout() {
  return (
    <QueryProvider>
      <Stack>
        <Stack.Screen name="(tabs)" options={{ headerShown: false }} />
        <Stack.Screen name="(auth)" options={{ headerShown: false }} />
      </Stack>
      <StatusBar style="auto" />
    </QueryProvider>
  );
}
```

#### 4.7 Update Tasks Screen with Server State
Update `app/(tabs)/tasks.tsx` to use TanStack Query:
```tsx
import { View, Text, FlatList, Pressable, ActivityIndicator } from 'react-native';
import { useAtomValue } from 'jotai';
import { userAtom } from '../../store/auth';
import { useTasks, useUpdateTask, useCreateTask } from '../../hooks/useTasks';
import { ApiTask } from '../../services/api';

const categories = [
  { id: 'all', label: 'All', color: 'bg-gray-500' },
  { id: 'work', label: 'Work', color: 'bg-blue-500' },
  { id: 'personal', label: 'Personal', color: 'bg-green-500' },
  { id: 'shopping', label: 'Shopping', color: 'bg-yellow-500' },
  { id: 'health', label: 'Health', color: 'bg-red-500' },
];

export default function TasksScreen() {
  const user = useAtomValue(userAtom);
  const { data: tasks = [], isLoading, error } = useTasks(user?.id || '');
  const updateTaskMutation = useUpdateTask();
  const createTaskMutation = useCreateTask();

  const toggleTask = (task: ApiTask) => {
    updateTaskMutation.mutate({
      taskId: task.id,
      updates: { completed: !task.completed }
    });
  };

  const addSampleTask = () => {
    if (!user) return;
    
    createTaskMutation.mutate({
      title: 'New Task from Server',
      description: 'This task is created via API',
      completed: false,
      category: 'work',
      priority: 'medium',
      userId: user.id,
    });
  };

  if (isLoading) {
    return (
      <View className="flex-1 justify-center items-center bg-gray-50">
        <ActivityIndicator size="large" color="#3b82f6" />
        <Text className="text-gray-600 mt-4">Loading tasks...</Text>
      </View>
    );
  }

  if (error) {
    return (
      <View className="flex-1 justify-center items-center bg-gray-50">
        <Text className="text-red-600 text-lg">Error loading tasks</Text>
        <Text className="text-gray-600">{error.message}</Text>
      </View>
    );
  }

  const renderTask = ({ item }: { item: ApiTask }) => (
    <View className="bg-white p-4 mb-3 rounded-lg shadow-sm border border-gray-100">
      <View className="flex-row items-center justify-between">
        <View className="flex-1">
          <Text className={`text-lg font-semibold ${
            item.completed ? 'text-gray-500 line-through' : 'text-gray-900'
          }`}>
            {item.title}
          </Text>
          {item.description && (
            <Text className="text-gray-600 mt-1">{item.description}</Text>
          )}
          <View className="flex-row items-center mt-2">
            <View className={`px-2 py-1 rounded-full mr-2 ${
              categories.find(c => c.id === item.category)?.color || 'bg-gray-500'
            }`}>
              <Text className="text-white text-xs font-medium">
                {categories.find(c => c.id === item.category)?.label}
              </Text>
            </View>
            <View className={`px-2 py-1 rounded-full ${
              item.priority === 'high' ? 'bg-red-100' : 
              item.priority === 'medium' ? 'bg-yellow-100' : 'bg-green-100'
            }`}>
              <Text className={`text-xs font-medium ${
                item.priority === 'high' ? 'text-red-800' : 
                item.priority === 'medium' ? 'text-yellow-800' : 'text-green-800'
              }`}>
                {item.priority}
              </Text>
            </View>
          </View>
        </View>
        <Pressable
          onPress={() => toggleTask(item)}
          disabled={updateTaskMutation.isPending}
          className={`w-6 h-6 rounded-full border-2 ${
            item.completed 
              ? 'bg-green-500 border-green-500' 
              : 'border-gray-300'
          } ${updateTaskMutation.isPending ? 'opacity-50' : ''}`}
        >
          {item.completed && (
            <Text className="text-white text-center text-xs leading-5">✓</Text>
          )}
        </Pressable>
      </View>
    </View>
  );

  return (
    <View className="flex-1 bg-gray-50">
      <View className="p-4">
        <Text className="text-xl font-bold text-gray-900 mb-4">My Tasks</Text>
        <Text className="text-gray-600 mb-4">
          {tasks.length} tasks • {tasks.filter(t => t.completed).length} completed
        </Text>
      </View>

      <View className="flex-1 px-4">
        {tasks.length === 0 ? (
          <View className="flex-1 justify-center items-center">
            <Text className="text-gray-500 text-lg mb-4">No tasks yet</Text>
            <Pressable
              onPress={addSampleTask}
              disabled={createTaskMutation.isPending}
              className="bg-primary-500 px-6 py-3 rounded-lg disabled:opacity-50"
            >
              <Text className="text-white font-semibold">
                {createTaskMutation.isPending ? 'Adding...' : 'Add Sample Task'}
              </Text>
            </Pressable>
          </View>
        ) : (
          <FlatList
            data={tasks}
            keyExtractor={(item) => item.id}
            renderItem={renderTask}
            showsVerticalScrollIndicator={false}
          />
        )}
      </View>
    </View>
  );
}
```

### ✅ Checkpoint 4
- [ ] TanStack Query is properly configured
- [ ] API calls work with loading states
- [ ] Mutations update cache optimistically
- [ ] Error handling is implemented
- [ ] Data persistence works correctly

**Resources:**
- [TanStack Query Official Documentation](https://tanstack.com/query/latest)
- [TanStack Query React Native Guide](https://tanstack.com/query/latest/docs/framework/react/react-native)

---

## 🔐 Exercise 5: Authentication Flow

### Goal
Implement a complete authentication flow with login, registration, and protected routes.

### Tasks

#### 5.1 Create Login Screen
Update `app/(auth)/login.tsx`:
```tsx
import { useState } from 'react';
import { View, Text, TextInput, Pressable, Alert } from 'react-native';
import { Link, router } from 'expo-router';
import { useLogin } from '../../hooks/useAuth';

export default function LoginScreen() {
  const [email, setEmail] = useState('demo@example.com');
  const [password, setPassword] = useState('password');
  const loginMutation = useLogin();

  const handleLogin = async () => {
    if (!email || !password) {
      Alert.alert('Error', 'Please fill in all fields');
      return;
    }

    try {
      await loginMutation.mutateAsync({ email, password });
      router.replace('/(tabs)/tasks');
    } catch (error) {
      Alert.alert('Login Failed', error.message || 'Invalid credentials');
    }
  };

  return (
    <View className="flex-1 bg-gray-50 justify-center p-6">
      <View className="bg-white rounded-xl p-8 shadow-lg">
        <Text className="text-3xl font-bold text-gray-900 text-center mb-8">
          Welcome Back
        </Text>

        <View className="mb-4">
          <Text className="text-gray-700 font-medium mb-2">Email</Text>
          <TextInput
            value={email}
            onChangeText={setEmail}
            placeholder="Enter your email"
            keyboardType="email-address"
            autoCapitalize="none"
            className="border border-gray-300 rounded-lg px-4 py-3 text-gray-900"
          />
        </View>

        <View className="mb-6">
          <Text className="text-gray-700 font-medium mb-2">Password</Text>
          <TextInput
            value={password}
            onChangeText={setPassword}
            placeholder="Enter your password"
            secureTextEntry
            className="border border-gray-300 rounded-lg px-4 py-3 text-gray-900"
          />
        </View>

        <Pressable
          onPress={handleLogin}
          disabled={loginMutation.isPending}
          className="bg-primary-500 rounded-lg py-4 mb-4 disabled:opacity-50"
        >
          <Text className="text-white text-center font-semibold text-lg">
            {loginMutation.isPending ? 'Signing In...' : 'Sign In'}
          </Text>
        </Pressable>

        <View className="flex-row justify-center">
          <Text className="text-gray-600">Don't have an account? </Text>
          <Link href="/(auth)/register" asChild>
            <Pressable>
              <Text className="text-primary-500 font-semibold">Sign Up</Text>
            </Pressable>
          </Link>
        </View>
      </View>
    </View>
  );
}
```

#### 5.2 Create Register Screen
Create `app/(auth)/register.tsx`:
```tsx
import { useState } from 'react';
import { View, Text, TextInput, Pressable, Alert } from 'react-native';
import { Link, router } from 'expo-router';
import { useRegister } from '../../hooks/useAuth';

export default function RegisterScreen() {
  const [name, setName] = useState('');
  const [email, setEmail] = useState('');
  const [password, setPassword] = useState('');
  const [confirmPassword, setConfirmPassword] = useState('');
  const registerMutation = useRegister();

  const handleRegister = async () => {
    if (!name || !email || !password || !confirmPassword) {
      Alert.alert('Error', 'Please fill in all fields');
      return;
    }

    if (password !== confirmPassword) {
      Alert.alert('Error', 'Passwords do not match');
      return;
    }

    if (password.length < 6) {
      Alert.alert('Error', 'Password must be at least 6 characters');
      return;
    }

    try {
      await registerMutation.mutateAsync({ name, email, password });
      router.replace('/(tabs)/tasks');
    } catch (error) {
      Alert.alert('Registration Failed', error.message || 'Please try again');
    }
  };

  return (
    <View className="flex-1 bg-gray-50 justify-center p-6">
      <View className="bg-white rounded-xl p-8 shadow-lg">
        <Text className="text-3xl font-bold text-gray-900 text-center mb-8">
          Create Account
        </Text>

        <View className="mb-4">
          <Text className="text-gray-700 font-medium mb-2">Full Name</Text>
          <TextInput
            value={name}
            onChangeText={setName}
            placeholder="Enter your full name"
            className="border border-gray-300 rounded-lg px-4 py-3 text-gray-900"
          />
        </View>

        <View className="mb-4">
          <Text className="text-gray-700 font-medium mb-2">Email</Text>
          <TextInput
            value={email}
            onChangeText={setEmail}
            placeholder="Enter your email"
            keyboardType="email-address"
            autoCapitalize="none"
            className="border border-gray-300 rounded-lg px-4 py-3 text-gray-900"
          />
        </View>

        <View className="mb-4">
          <Text className="text-gray-700 font-medium mb-2">Password</Text>
          <TextInput
            value={password}
            onChangeText={setPassword}
            placeholder="Create a password"
            secureTextEntry
            className="border border-gray-300 rounded-lg px-4 py-3 text-gray-900"
          />
        </View>

        <View className="mb-6">
          <Text className="text-gray-700 font-medium mb-2">Confirm Password</Text>
          <TextInput
            value={confirmPassword}
            onChangeText={setConfirmPassword}
            placeholder="Confirm your password"
            secureTextEntry
            className="border border-gray-300 rounded-lg px-4 py-3 text-gray-900"
          />
        </View>

        <Pressable
          onPress={handleRegister}
          disabled={registerMutation.isPending}
          className="bg-primary-500 rounded-lg py-4 mb-4 disabled:opacity-50"
        >
          <Text className="text-white text-center font-semibold text-lg">
            {registerMutation.isPending ? 'Creating Account...' : 'Create Account'}
          </Text>
        </Pressable>

        <View className="flex-row justify-center">
          <Text className="text-gray-600">Already have an account? </Text>
          <Link href="/(auth)/login" asChild>
            <Pressable>
              <Text className="text-primary-500 font-semibold">Sign In</Text>
            </Pressable>
          </Link>
        </View>
      </View>
    </View>
  );
}
```

#### 5.3 Create Auth Layout
Create `app/(auth)/_layout.tsx`:
```tsx
import { Stack } from 'expo-router';

export default function AuthLayout() {
  return (
    <Stack screenOptions={{ headerShown: false }}>
      <Stack.Screen name="login" />
      <Stack.Screen name="register" />
    </Stack>
  );
}
```

#### 5.4 Create Profile Screen
Update `app/(tabs)/profile.tsx`:
```tsx
import { View, Text, Pressable, Alert } from 'react-native';
import { router } from 'expo-router';
import { useAtom, useAtomValue } from 'jotai';
import { userAtom, logoutAtom } from '../../store/auth';
import { useProfile } from '../../hooks/useAuth';

export default function ProfileScreen() {
  const [user, setUser] = useAtom(userAtom);
  const logout = useAtomValue(logoutAtom);
  const { data: profile, isLoading } = useProfile(user?.id || '');

  const handleLogout = () => {
    Alert.alert(
      'Logout',
      'Are you sure you want to logout?',
      [
        { text: 'Cancel', style: 'cancel' },
        {
          text: 'Logout',
          style: 'destructive',
          onPress: () => {
            setUser(null);
            router.replace('/');
          },
        },
      ]
    );
  };

  if (!user) {
    return (
      <View className="flex-1 bg-gray-50 justify-center items-center p-6">
        <Text className="text-gray-500 text-lg mb-4">Please sign in</Text>
        <Pressable
          onPress={() => router.push('/(auth)/login')}
          className="bg-primary-500 px-6 py-3 rounded-lg"
        >
          <Text className="text-white font-semibold">Sign In</Text>
        </Pressable>
      </View>
    );
  }

  return (
    <View className="flex-1 bg-gray-50">
      <View className="bg-white p-6 shadow-sm">
        <Text className="text-2xl font-bold text-gray-900 mb-2">Profile</Text>
        <Text className="text-gray-600">Manage your account settings</Text>
      </View>

      <View className="p-6">
        <View className="bg-white rounded-xl p-6 shadow-sm mb-6">
          <View className="items-center mb-6">
            <View className="w-20 h-20 bg-primary-500 rounded-full justify-center items-center mb-4">
              <Text className="text-white text-2xl font-bold">
                {user.name.charAt(0).toUpperCase()}
              </Text>
            </View>
            <Text className="text-xl font-bold text-gray-900">{user.name}</Text>
            <Text className="text-gray-600">{user.email}</Text>
          </View>

          <View className="space-y-4">
            <View className="border-b border-gray-100 pb-4">
              <Text className="text-gray-700 font-medium mb-1">Full Name</Text>
              <Text className="text-gray-900">{profile?.name || user.name}</Text>
            </View>

            <View className="border-b border-gray-100 pb-4">
              <Text className="text-gray-700 font-medium mb-1">Email</Text>
              <Text className="text-gray-900">{profile?.email || user.email}</Text>
            </View>

            <View>
              <Text className="text-gray-700 font-medium mb-1">User ID</Text>
              <Text className="text-gray-500 text-sm">{user.id}</Text>
            </View>
          </View>
        </View>

        <View className="bg-white rounded-xl p-6 shadow-sm mb-6">
          <Text className="text-lg font-semibold text-gray-900 mb-4">Account Actions</Text>
          
          <Pressable className="flex-row items-center py-3 border-b border-gray-100">
            <Text className="text-gray-700 flex-1">Edit Profile</Text>
            <Text className="text-gray-400">›</Text>
          </Pressable>
          
          <Pressable className="flex-row items-center py-3 border-b border-gray-100">
            <Text className="text-gray-700 flex-1">Notifications</Text>
            <Text className="text-gray-400">›</Text>
          </Pressable>
          
          <Pressable className="flex-row items-center py-3">
            <Text className="text-gray-700 flex-1">Privacy Settings</Text>
            <Text className="text-gray-400">›</Text>
          </Pressable>
        </View>

        <Pressable
          onPress={handleLogout}
          className="bg-red-500 rounded-lg py-4"
        >
          <Text className="text-white text-center font-semibold text-lg">
            Logout
          </Text>
        </Pressable>
      </View>
    </View>
  );
}
```

### ✅ Checkpoint 5
- [ ] Login and registration forms work
- [ ] Authentication state persists
- [ ] Protected routes redirect properly
- [ ] Profile screen displays user data
- [ ] Logout functionality works

**Resources:**
- [Expo Router Authentication](https://docs.expo.dev/router/reference/authentication/)
- [DEV Community: Using TypeScript with React Native Best Practices](https://dev.to/aneeqakhan/using-typescript-with-react-native-best-practices-62)

---

## 🎨 Exercise 6: Advanced UI Components & Animations

### Goal
Create polished UI components with smooth animations and gestures.

### Tasks

#### 6.1 Install Animation Libraries
```bash
npx expo install react-native-reanimated react-native-gesture-handler
```

#### 6.2 Create Task Creation Modal
Create `components/TaskModal.tsx`:
```tsx
import { useState } from 'react';
import {
  View,
  Text,
  TextInput,
  Pressable,
  Modal,
  ScrollView,
  Alert,
} from 'react-native';
import { useCreateTask } from '../hooks/useTasks';
import { useAtomValue } from 'jotai';
import { userAtom } from '../store/auth';

interface TaskModalProps {
  visible: boolean;
  onClose: () => void;
}

const categories = [
  { id: 'work', label: 'Work', color: 'bg-blue-500' },
  { id: 'personal', label: 'Personal', color: 'bg-green-500' },
  { id: 'shopping', label: 'Shopping', color: 'bg-yellow-500' },
  { id: 'health', label: 'Health', color: 'bg-red-500' },
];

const priorities = [
  { id: 'low', label: 'Low Priority', color: 'bg-green-100 text-green-800' },
  { id: 'medium', label: 'Medium Priority', color: 'bg-yellow-100 text-yellow-800' },
  { id: 'high', label: 'High Priority', color: 'bg-red-100 text-red-800' },
];

export default function TaskModal({ visible, onClose }: TaskModalProps) {
  const [title, setTitle] = useState('');
  const [description, setDescription] = useState('');
  const [category, setCategory] = useState<'work' | 'personal' | 'shopping' | 'health'>('work');
  const [priority, setPriority] = useState<'low' | 'medium' | 'high'>('medium');

  const user = useAtomValue(userAtom);
  const createTaskMutation = useCreateTask();

  const handleSubmit = async () => {
    if (!title.trim()) {
      Alert.alert('Error', 'Please enter a task title');
      return;
    }

    if (!user) {
      Alert.alert('Error', 'Please log in to create tasks');
      return;
    }

    try {
      await createTaskMutation.mutateAsync({
        title: title.trim(),
        description: description.trim() || undefined,
        completed: false,
        category,
        priority,
        userId: user.id,
      });

      // Reset form
      setTitle('');
      setDescription('');
      setCategory('work');
      setPriority('medium');
      onClose();
    } catch (error) {
      Alert.alert('Error', 'Failed to create task. Please try again.');
    }
  };

  return (
    <Modal
      visible={visible}
      animationType="slide"
      presentationStyle="pageSheet"
      onRequestClose={onClose}
    >
      <View className="flex-1 bg-gray-50">
        {/* Header */}
        <View className="bg-white p-6 shadow-sm">
          <View className="flex-row items-center justify-between">
            <Pressable onPress={onClose}>
              <Text className="text-primary-500 text-lg font-medium">Cancel</Text>
            </Pressable>
            <Text className="text-xl font-bold text-gray-900">New Task</Text>
            <Pressable
              onPress={handleSubmit}
              disabled={createTaskMutation.isPending || !title.trim()}
              className="disabled:opacity-50"
            >
              <Text className="text-primary-500 text-lg font-medium">
                {createTaskMutation.isPending ? 'Saving...' : 'Save'}
              </Text>
            </Pressable>
          </View>
        </View>

        <ScrollView className="flex-1 p-6">
          {/* Title Input */}
          <View className="mb-6">
            <Text className="text-gray-700 font-medium mb-2">Title *</Text>
            <TextInput
              value={title}
              onChangeText={setTitle}
              placeholder="Enter task title"
              className="bg-white border border-gray-300 rounded-lg px-4 py-3 text-gray-900"
              multiline
            />
          </View>

          {/* Description Input */}
          <View className="mb-6">
            <Text className="text-gray-700 font-medium mb-2">Description</Text>
            <TextInput
              value={description}
              onChangeText={setDescription}
              placeholder="Enter task description (optional)"
              className="bg-white border border-gray-300 rounded-lg px-4 py-3 text-gray-900 h-24"
              multiline
              textAlignVertical="top"
            />
          </View>

          {/* Category Selection */}
          <View className="mb-6">
            <Text className="text-gray-700 font-medium mb-3">Category</Text>
            <View className="flex-row flex-wrap gap-3">
              {categories.map((cat) => (
                <Pressable
                  key={cat.id}
                  onPress={() => setCategory(cat.id as any)}
                  className={`px-4 py-2 rounded-full ${
                    category === cat.id ? cat.color : 'bg-gray-200'
                  }`}
                >
                  <Text className={`font-medium ${
                    category === cat.id ? 'text-white' : 'text-gray-700'
                  }`}>
                    {cat.label}
                  </Text>
                </Pressable>
              ))}
            </View>
          </View>

          {/* Priority Selection */}
          <View className="mb-6">
            <Text className="text-gray-700 font-medium mb-3">Priority</Text>
            <View className="space-y-2">
              {priorities.map((prio) => (
                <Pressable
                  key={prio.id}
                  onPress={() => setPriority(prio.id as any)}
                  className={`p-4 rounded-lg border-2 ${
                    priority === prio.id
                      ? 'border-primary-500 bg-primary-50'
                      : 'border-gray-200 bg-white'
                  }`}
                >
                  <View className="flex-row items-center justify-between">
                    <Text className={`font-medium ${
                      priority === prio.id ? 'text-primary-900' : 'text-gray-900'
                    }`}>
                      {prio.label}
                    </Text>
                    <View className={`px-2 py-1 rounded-full ${prio.color}`}>
                      <Text className="text-xs font-medium">
                        {prio.id.toUpperCase()}
                      </Text>
                    </View>
                  </View>
                </Pressable>
              ))}
            </View>
          </View>
        </ScrollView>
      </View>
    </Modal>
  );
}
```

#### 6.3 Add Floating Action Button
Create `components/FloatingActionButton.tsx`:
```tsx
import { Pressable, Text } from 'react-native';
import Animated, {
  useSharedValue,
  useAnimatedStyle,
  withSpring,
  withTiming,
} from 'react-native-reanimated';

interface FloatingActionButtonProps {
  onPress: () => void;
}

const AnimatedPressable = Animated.createAnimatedComponent(Pressable);

export default function FloatingActionButton({ onPress }: FloatingActionButtonProps) {
  const scale = useSharedValue(1);
  const opacity = useSharedValue(1);

  const animatedStyle = useAnimatedStyle(() => ({
    transform: [{ scale: scale.value }],
    opacity: opacity.value,
  }));

  const handlePressIn = () => {
    scale.value = withSpring(0.9);
  };

  const handlePressOut = () => {
    scale.value = withSpring(1);
  };

  return (
    <AnimatedPressable
      onPress={onPress}
      onPressIn={handlePressIn}
      onPressOut={handlePressOut}
      style={animatedStyle}
      className="absolute bottom-6 right-6 w-14 h-14 bg-primary-500 rounded-full shadow-lg justify-center items-center"
    >
      <Text className="text-white text-2xl font-light">+</Text>
    </AnimatedPressable>
  );
}
```

#### 6.4 Update Tasks Screen with New Components
Update `app/(tabs)/tasks.tsx` to include the modal and FAB:
```tsx
import { useState } from 'react';
import { View, Text, FlatList, Pressable, ActivityIndicator } from 'react-native';
import { useAtomValue } from 'jotai';
import { userAtom } from '../../store/auth';
import { useTasks, useUpdateTask } from '../../hooks/useTasks';
import { ApiTask } from '../../services/api';
import TaskModal from '../../components/TaskModal';
import FloatingActionButton from '../../components/FloatingActionButton';

// ... (previous code for categories, etc.)

export default function TasksScreen() {
  const [modalVisible, setModalVisible] = useState(false);
  const user = useAtomValue(userAtom);
  const { data: tasks = [], isLoading, error } = useTasks(user?.id || '');
  const updateTaskMutation = useUpdateTask();

  // ... (previous code for toggleTask, etc.)

  return (
    <View className="flex-1 bg-gray-50">
      {/* ... (previous render code) */}

      <FloatingActionButton onPress={() => setModalVisible(true)} />
      
      <TaskModal
        visible={modalVisible}
        onClose={() => setModalVisible(false)}
      />
    </View>
  );
}
```

### ✅ Checkpoint 6
- [ ] Task creation modal works smoothly
- [ ] Floating action button has proper animations
- [ ] Form validation prevents empty tasks
- [ ] Modal presentations are smooth
- [ ] Animations feel natural and responsive

**Resources:**
- [React Native Reanimated Documentation](https://docs.swmansion.com/react-native-reanimated/)
- [Expo Router Modals](https://docs.expo.dev/router/advanced/modals/)

---

## 🎯 Final Exercise: Polish & Testing

### Goal
Add final touches, error boundaries, and test the complete application.

### Tasks

#### 7.1 Add Error Boundary
Create `components/ErrorBoundary.tsx`:
```tsx
import React from 'react';
import { View, Text, Pressable } from 'react-native';

interface Props {
  children: React.ReactNode;
}

interface State {
  hasError: boolean;
  error?: Error;
}

export default class ErrorBoundary extends React.Component<Props, State> {
  constructor(props: Props) {
    super(props);
    this.state = { hasError: false };
  }

  static getDerivedStateFromError(error: Error): State {
    return { hasError: true, error };
  }

  componentDidCatch(error: Error, errorInfo: React.ErrorInfo) {
    console.error('ErrorBoundary caught an error:', error, errorInfo);
  }

  render() {
    if (this.state.hasError) {
      return (
        <View className="flex-1 justify-center items-center bg-gray-50 p-6">
          <Text className="text-xl font-bold text-gray-900 mb-4">
            Oops! Something went wrong
          </Text>
          <Text className="text-gray-600 text-center mb-6">
            The app encountered an unexpected error. Please try restarting the application.
          </Text>
          <Pressable
            onPress={() => this.setState({ hasError: false, error: undefined })}
            className="bg-primary-500 px-6 py-3 rounded-lg"
          >
            <Text className="text-white font-semibold">Try Again</Text>
          </Pressable>
        </View>
      );
    }

    return this.props.children;
  }
}
```

#### 7.2 Add Loading States and Empty States
Create `components/EmptyState.tsx`:
```tsx
import { View, Text, Pressable } from 'react-native';

interface EmptyStateProps {
  title: string;
  description: string;
  actionText?: string;
  onAction?: () => void;
}

export default function EmptyState({ 
  title, 
  description, 
  actionText, 
  onAction 
}: EmptyStateProps) {
  return (
    <View className="flex-1 justify-center items-center p-8">
      <Text className="text-xl font-bold text-gray-900 mb-2 text-center">
        {title}
      </Text>
      <Text className="text-gray-600 text-center mb-6">
        {description}
      </Text>
      {actionText && onAction && (
        <Pressable
          onPress={onAction}
          className="bg-primary-500 px-6 py-3 rounded-lg"
        >
          <Text className="text-white font-semibold">{actionText}</Text>
        </Pressable>
      )}
    </View>
  );
}
```

#### 7.3 Update Root Layout with Error Boundary
Update `app/_layout.tsx`:
```tsx
import { Stack } from 'expo-router';
import { StatusBar } from 'expo-status-bar';
import QueryProvider from '../providers/QueryProvider';
import ErrorBoundary from '../components/ErrorBoundary';

export default function RootLayout() {
  return (
    <ErrorBoundary>
      <QueryProvider>
        <Stack>
          <Stack.Screen name="(tabs)" options={{ headerShown: false }} />
          <Stack.Screen name="(auth)" options={{ headerShown: false }} />
        </Stack>
        <StatusBar style="auto" />
      </QueryProvider>
    </ErrorBoundary>
  );
}
```

#### 7.4 Add Tab Layout
Create `app/(tabs)/_layout.tsx`:
```tsx
import { Tabs } from 'expo-router';
import { Text } from 'react-native';

export default function TabLayout() {
  return (
    <Tabs
      screenOptions={{
        headerShown: false,
        tabBarActiveTintColor: '#3b82f6',
        tabBarInactiveTintColor: '#6b7280',
        tabBarStyle: {
          backgroundColor: '#ffffff',
          borderTopWidth: 1,
          borderTopColor: '#e5e7eb',
        },
      }}
    >
      <Tabs.Screen
        name="tasks"
        options={{
          title: 'Tasks',
          tabBarIcon: ({ color }) => (
            <Text style={{ color, fontSize: 18 }}>✓</Text>
          ),
        }}
      />
      <Tabs.Screen
        name="profile"
        options={{
          title: 'Profile',
          tabBarIcon: ({ color }) => (
            <Text style={{ color, fontSize: 18 }}>👤</Text>
          ),
        }}
      />
      <Tabs.Screen
        name="settings"
        options={{
          title: 'Settings',
          tabBarIcon: ({ color }) => (
            <Text style={{ color, fontSize: 18 }}>⚙️</Text>
          ),
        }}
      />
    </Tabs>
  );
}
```

#### 7.5 Create Settings Screen
Create `app/(tabs)/settings.tsx`:
```tsx
import { View, Text, Pressable, Switch } from 'react-native';
import { useState } from 'react';
import { useAtom } from 'jotai';
import { themeAtom } from '../../store/theme';

export default function SettingsScreen() {
  const [theme, setTheme] = useAtom(themeAtom);
  const [notificationsEnabled, setNotificationsEnabled] = useState(true);
  const [syncEnabled, setSyncEnabled] = useState(true);

  return (
    <View className="flex-1 bg-gray-50">
      <View className="bg-white p-6 shadow-sm">
        <Text className="text-2xl font-bold text-gray-900 mb-2">Settings</Text>
        <Text className="text-gray-600">Customize your app experience</Text>
      </View>

      <View className="p-6">
        {/* Appearance Section */}
        <View className="bg-white rounded-xl p-6 shadow-sm mb-6">
          <Text className="text-lg font-semibold text-gray-900 mb-4">Appearance</Text>
          
          <View className="flex-row items-center justify-between py-3">
            <View>
              <Text className="text-gray-900 font-medium">Dark Mode</Text>
              <Text className="text-gray-500 text-sm">Switch to dark theme</Text>
            </View>
            <Switch
              value={theme === 'dark'}
              onValueChange={(value) => setTheme(value ? 'dark' : 'light')}
            />
          </View>
        </View>

        {/* Notifications Section */}
        <View className="bg-white rounded-xl p-6 shadow-sm mb-6">
          <Text className="text-lg font-semibold text-gray-900 mb-4">Notifications</Text>
          
          <View className="flex-row items-center justify-between py-3">
            <View>
              <Text className="text-gray-900 font-medium">Push Notifications</Text>
              <Text className="text-gray-500 text-sm">Get notified about updates</Text>
            </View>
            <Switch
              value={notificationsEnabled}
              onValueChange={setNotificationsEnabled}
            />
          </View>
        </View>

        {/* Sync Section */}
        <View className="bg-white rounded-xl p-6 shadow-sm mb-6">
          <Text className="text-lg font-semibold text-gray-900 mb-4">Data & Sync</Text>
          
          <View className="flex-row items-center justify-between py-3">
            <View>
              <Text className="text-gray-900 font-medium">Auto Sync</Text>
              <Text className="text-gray-500 text-sm">Sync data automatically</Text>
            </View>
            <Switch
              value={syncEnabled}
              onValueChange={setSyncEnabled}
            />
          </View>
        </View>

        {/* About Section */}
        <View className="bg-white rounded-xl p-6 shadow-sm">
          <Text className="text-lg font-semibold text-gray-900 mb-4">About</Text>
          
          <View className="space-y-3">
            <View>
              <Text className="text-gray-700 font-medium">Version</Text>
              <Text className="text-gray-500">1.0.0</Text>
            </View>
            
            <View>
              <Text className="text-gray-700 font-medium">Build</Text>
              <Text className="text-gray-500">2025.01.01</Text>
            </View>
          </View>
        </View>
      </View>
    </View>
  );
}
```

### ✅ Final Checkpoint
Test your complete application:

- [ ] **Navigation**: All screens accessible via file-based routing
- [ ] **Authentication**: Login/register/logout flow works
- [ ] **Task Management**: Create, read, update, delete tasks
- [ ] **State Management**: Jotai atoms update correctly
- [ ] **Server State**: TanStack Query caching and mutations work
- [ ] **UI/UX**: NativeWind styling looks professional
- [ ] **Error Handling**: Error boundaries catch and display errors
- [ ] **Loading States**: Proper loading indicators
- [ ] **TypeScript**: No type errors in compilation
- [ ] **Performance**: Smooth animations and interactions

---

## 🏆 Congratulations!

You've successfully built a complete React Native app using the modern tech stack:

### What You've Learned
- ✅ **React Native + Expo**: Cross-platform mobile development
- ✅ **Expo Router**: File-based routing and navigation
- ✅ **TypeScript**: Type-safe development practices
- ✅ **Jotai**: Atomic state management
- ✅ **TanStack Query**: Server state and data fetching
- ✅ **NativeWind**: Utility-first styling
- ✅ **Animations**: Smooth user interactions
- ✅ **Authentication**: Complete auth flow
- ✅ **Error Handling**: Robust error boundaries

### Next Steps
1. **Deploy your app** using EAS Build
2. **Add more features** like push notifications, offline support
3. **Write unit tests** for critical components
4. **Implement real API endpoints** instead of mock data
5. **Add accessibility features** for better user experience
6. **Optimize performance** with React Native profiling tools

### Additional Features to Explore
- Push notifications with Expo Notifications
- Image upload with Expo ImagePicker
- Local storage with Expo SecureStore
- Geolocation with Expo Location
- Social authentication with Expo AuthSession
- Real-time updates with WebSockets

You now have a solid foundation in the React Native ecosystem and modern mobile development practices!
