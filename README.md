# 📱 คู่มือการสร้างแอป IT Worker ด้วย React Native + Expo Router

## 🎯 เกี่ยวกับโปรเจค

แอปพลิเคชันค้นหางานในสายอาชีพ IT ที่พัฒนาด้วย:
- ⚛️ React Native
- 📘 TypeScript  
- 🎨 Nativewind (Tailwind CSS)
- 🧭 Expo Router (File-based Routing)

---

## 📋 สารบัญ

1. [สร้างโปรเจคและติดตั้ง Expo Router](#step1)
2. [ทำความเข้าใจ Expo Router](#step2)
3. [ตั้งค่าโปรเจคให้พร้อมใช้งาน](#step3)
4. [สร้างโครงสร้างโฟลเดอร์](#step4)
5. [สร้างไฟล์ Types และ Data](#step5)
6. [สร้าง Component](#step6)
7. [สร้างหน้าจอด้วย Expo Router](#step7)
8. [การรันโปรเจค](#step8)
9. [การแก้ปัญหา](#troubleshooting)
10. [สรุปและเอกสารอ้างอิง](#summary)

---

## <a name="step1">1. สร้างโปรเจคและติดตั้ง Expo Router</a>

### 1.1 สร้างโปรเจค

```bash
npx rn-new it-worker --nativewind
cd it-worker
```

**หมายเหตุ:** โปรเจคที่สร้างจะมีโครงสร้างแบบนี้:

```
it-worker/
├── assets/
├── components/
├── node_modules/
├── App.tsx              ← ไฟล์หลัก (จะลบทิ้งในขั้นตอนถัดไป)
├── app.json
├── babel.config.js
├── package.json
├── tailwind.config.js
└── tsconfig.json
```

### 1.2 ติดตั้ง Expo Router และ Dependencies

```bash
npx expo install expo-router react-native-safe-area-context react-native-screens expo-linking expo-constants expo-status-bar
```

**รอให้การติดตั้งเสร็จสิ้น** (ประมาณ 1-2 นาที)

**อ้างอิง:**
- [Expo Router - Installation](https://docs.expo.dev/router/installation/)
- [Nativewind Documentation](https://www.nativewind.dev/)

---

## <a name="step2">2. ทำความเข้าใจ Expo Router</a>

### 🎯 Expo Router คืออะไร?

Expo Router คือระบบ Navigation แบบ **File-based Routing** เหมือน Next.js

### ⭐ คุณสมบัติหลัก

#### 2.1 File-based Routing

```
app/
├── _layout.tsx         →  Layout หลัก
├── index.tsx           →  หน้าแรก (/)
└── job/
    └── [id].tsx       →  หน้ารายละเอียด (/job/1, /job/2)
```

**ข้อดี:**
- ✅ ไม่ต้องตั้งค่า Navigation ซับซ้อน
- ✅ ชื่อไฟล์ = URL path
- ✅ เข้าใจง่าย ดูแลง่าย

#### 2.2 Dynamic Routes

```typescript
// app/job/[id].tsx
import { useLocalSearchParams } from 'expo-router';

export default function JobDetail() {
  const { id } = useLocalSearchParams();
  // URL: /job/1 → id = "1"
}
```

#### 2.3 การนำทาง

```typescript
import { router } from 'expo-router';

// นำทางไปหน้าอื่น
router.push('/job/1');
```

**อ้างอิง:**
- [Expo Router - Introduction](https://docs.expo.dev/router/introduction/)
- [File-based Routing](https://docs.expo.dev/router/create-pages/)

---

## <a name="step3">3. ตั้งค่าโปรเจคให้พร้อมใช้งาน</a>

### 3.1 แก้ไข package.json

เปิดไฟล์ `package.json` และแก้ไขบรรทัด `"main"`:

```json
{
  "name": "it-worker",
  "version": "1.0.0",
  "main": "expo-router/entry",
  "scripts": {
    "start": "expo start",
    "android": "expo start --android",
    "ios": "expo start --ios",
    "web": "expo start --web"
  }
}
```

**เปลี่ยน:** 
- ❌ `"main": "node_modules/expo/AppEntry.js"`
- ✅ `"main": "expo-router/entry"`

### 3.2 ลบไฟล์ App.tsx

```bash
# สำหรับ Mac/Linux
rm App.tsx

# สำหรับ Windows (PowerShell)
Remove-Item App.tsx
```

**เหตุผล:** เราจะใช้ Expo Router แทน ซึ่งใช้โฟลเดอร์ `app/` แทนไฟล์ `App.tsx`

### 3.3 ตรวจสอบไฟล์ Config

#### ตรวจสอบ `babel.config.js`

```javascript
module.exports = function (api) {
  api.cache(true);
  return {
    presets: ['babel-preset-expo'],
    plugins: ['nativewind/babel'],
  };
};
```

#### ตรวจสอบ `tailwind.config.js`

```javascript
/** @type {import('tailwindcss').Config} */
module.exports = {
  content: [
    "./app/**/*.{js,jsx,ts,tsx}",
    "./components/**/*.{js,jsx,ts,tsx}"
  ],
  theme: {
    extend: {},
  },
  plugins: [],
}
```

---

## <a name="step4">4. สร้างโครงสร้างโฟลเดอร์</a>

### 4.1 สร้างโฟลเดอร์ทั้งหมด

```bash
# สร้างโฟลเดอร์หลัก
mkdir app
mkdir app/job
mkdir types
mkdir data

# โฟลเดอร์ components มีอยู่แล้ว (ไม่ต้องสร้าง)
```

### 4.2 โครงสร้างสุดท้าย

```
it-worker/
├── app/                     ← สร้างใหม่
│   ├── _layout.tsx         ← จะสร้างในขั้นตอนถัดไป
│   ├── index.tsx           ← จะสร้างในขั้นตอนถัดไป
│   └── job/                ← สร้างใหม่
│       └── [id].tsx        ← จะสร้างในขั้นตอนถัดไป
├── assets/
├── components/
│   └── JobCard.tsx         ← จะสร้างในขั้นตอนถัดไป
├── data/                    ← สร้างใหม่
│   └── jobs.ts             ← จะสร้างในขั้นตอนถัดไป
├── types/                   ← สร้างใหม่
│   └── job.ts              ← จะสร้างในขั้นตอนถัดไป
├── node_modules/
├── app.json
├── babel.config.js
├── package.json
├── tailwind.config.js
└── tsconfig.json
```

---

## <a name="step5">5. สร้างไฟล์ Types และ Data</a>

### 5.1 สร้างไฟล์ `types/job.ts`

```typescript
// types/job.ts
export interface Job {
  id: number;
  title: string;
  company: string;
  salary: string;
  location: string;
  description: string;
  requirements: string[];
  type: string;
}
```

### 5.2 สร้างไฟล์ `data/jobs.ts`

```typescript
// data/jobs.ts
import { Job } from '../types/job';

export const jobs: Job[] = [
  {
    id: 1,
    title: 'Frontend Developer',
    company: 'Tech Company A',
    salary: '40,000 - 60,000 บาท',
    location: 'กรุงเทพฯ',
    description: 'พัฒนาเว็บแอปพลิเคชันด้วย React และ TypeScript ร่วมกับทีมออกแบบ สร้าง UI ที่ทันสมัยและ responsive',
    requirements: [
      'มีประสบการณ์ React อย่างน้อย 2 ปี',
      'เชี่ยวชาญ HTML, CSS, JavaScript',
      'เข้าใจหลักการ Responsive Design',
      'มีประสบการณ์ใช้ Git'
    ],
    type: 'Full-time'
  },
  {
    id: 2,
    title: 'Backend Developer',
    company: 'Startup B',
    salary: '45,000 - 70,000 บาท',
    location: 'เชียงใหม่',
    description: 'พัฒนา API และระบบ Backend ด้วย Node.js รองรับผู้ใช้งานจำนวนมาก',
    requirements: [
      'เชี่ยวชาญ Node.js และ Express',
      'มีประสบการณ์ Database (MySQL/MongoDB)',
      'เข้าใจ RESTful API',
      'มีความรู้เรื่อง Security'
    ],
    type: 'Full-time'
  },
  {
    id: 3,
    title: 'Mobile Developer',
    company: 'Digital Agency C',
    salary: '35,000 - 55,000 บาท',
    location: 'ระยอง',
    description: 'พัฒนาแอปมือถือด้วย React Native สำหรับ iOS และ Android',
    requirements: [
      'มีประสบการณ์ React Native',
      'เข้าใจ Mobile UI/UX',
      'สามารถ Deploy แอปได้',
      'มีความรู้เรื่อง Push Notification'
    ],
    type: 'Full-time'
  },
  {
    id: 4,
    title: 'UI/UX Designer',
    company: 'Creative Studio D',
    salary: '30,000 - 50,000 บาท',
    location: 'กรุงเทพฯ',
    description: 'ออกแบบ User Interface และ User Experience สำหรับเว็บและแอปมือถือ',
    requirements: [
      'เชี่ยวชาญ Figma หรือ Adobe XD',
      'เข้าใจหลักการ Design Thinking',
      'มี Portfolio ผลงาน',
      'สามารถทำ User Research'
    ],
    type: 'Full-time'
  },
  {
    id: 5,
    title: 'Data Analyst',
    company: 'Finance Corp E',
    salary: '38,000 - 58,000 บาท',
    location: 'กรุงเทพฯ',
    description: 'วิเคราะห์ข้อมูลธุรกิจและสร้าง Dashboard สำหรับผู้บริหาร',
    requirements: [
      'เชี่ยวชาญ Excel, SQL',
      'มีประสบการณ์ Data Visualization',
      'เข้าใจ Business Intelligence',
      'มีความรู้เรื่อง Statistics'
    ],
    type: 'Full-time'
  },
  {
    id: 6,
    title: 'DevOps Engineer',
    company: 'Cloud Services F',
    salary: '50,000 - 80,000 บาท',
    location: 'กรุงเทพฯ',
    description: 'จัดการ Infrastructure และ CI/CD Pipeline ดูแลระบบ Cloud',
    requirements: [
      'มีประสบการณ์ AWS หรือ GCP',
      'เชี่ยวชาญ Docker และ Kubernetes',
      'เข้าใจ CI/CD Pipeline',
      'มีความรู้ Linux Administration'
    ],
    type: 'Full-time'
  }
];
```

---

## <a name="step6">6. สร้าง Component</a>

### สร้างไฟล์ `components/JobCard.tsx`

```typescript
// components/JobCard.tsx
import React from 'react';
import { View, Text, TouchableOpacity } from 'react-native';
import { Job } from '../types/job';

interface JobCardProps {
  job: Job;
  onPress: () => void;
}

export default function JobCard({ job, onPress }: JobCardProps) {
  return (
    <TouchableOpacity
      onPress={onPress}
      className="bg-white p-4 rounded-xl mb-3 shadow-sm border border-gray-200"
    >
      <Text className="text-lg font-bold text-gray-800 mb-1">
        {job.title}
      </Text>
      <Text className="text-sm text-gray-600 mb-3">
        {job.company}
      </Text>
      
      <View className="flex-row flex-wrap gap-2 mb-3">
        <View className="bg-blue-100 px-3 py-1 rounded-full">
          <Text className="text-xs text-blue-700 font-medium">
            {job.location}
          </Text>
        </View>
        <View className="bg-green-100 px-3 py-1 rounded-full">
          <Text className="text-xs text-green-700 font-medium">
            {job.type}
          </Text>
        </View>
      </View>
      
      <Text className="text-sm font-semibold text-orange-600">
        💰 {job.salary}
      </Text>
    </TouchableOpacity>
  );
}
```

---

## <a name="step7">7. สร้างหน้าจอด้วย Expo Router</a>

### 7.1 สร้างไฟล์ `app/_layout.tsx`

```typescript
// app/_layout.tsx
import { Stack } from 'expo-router';

export default function Layout() {
  return (
    <Stack
      screenOptions={{
        headerStyle: {
          backgroundColor: '#3b82f6',
        },
        headerTintColor: '#fff',
        headerTitleStyle: {
          fontWeight: 'bold',
        },
      }}
    >
      <Stack.Screen
        name="index"
        options={{
          title: '🚀 งานในสายอาชีพ IT',
        }}
      />
      <Stack.Screen
        name="job/[id]"
        options={{
          title: 'รายละเอียดงาน',
        }}
      />
    </Stack>
  );
}
```

### 7.2 สร้างไฟล์ `app/index.tsx`

```typescript
// app/index.tsx
import React from 'react';
import { View, Text, FlatList, SafeAreaView } from 'react-native';
import { router } from 'expo-router';
import JobCard from '../components/JobCard';
import { jobs } from '../data/jobs';

export default function Home() {
  return (
    <SafeAreaView className="flex-1 bg-gray-50">
      <View className="flex-1 px-4 pt-4">
        <View className="mb-4">
          <Text className="text-3xl font-bold text-gray-800 mb-2">
            งาน IT
          </Text>
          <Text className="text-base text-gray-600">
            พบ <Text className="font-semibold text-blue-600">{jobs.length}</Text> ตำแหน่งงาน
          </Text>
        </View>

        <FlatList
          data={jobs}
          keyExtractor={(item) => item.id.toString()}
          renderItem={({ item }) => (
            <JobCard
              job={item}
              onPress={() => router.push(`/job/${item.id}`)}
            />
          )}
          showsVerticalScrollIndicator={false}
          contentContainerStyle={{ paddingBottom: 20 }}
        />
      </View>
    </SafeAreaView>
  );
}
```

### 7.3 สร้างไฟล์ `app/job/[id].tsx`

```typescript
// app/job/[id].tsx
import React from 'react';
import { View, Text, ScrollView, SafeAreaView } from 'react-native';
import { useLocalSearchParams } from 'expo-router';
import { jobs } from '../../data/jobs';

export default function JobDetail() {
  const { id } = useLocalSearchParams();
  const job = jobs.find((j) => j.id === Number(id));

  if (!job) {
    return (
      <SafeAreaView className="flex-1 bg-white">
        <View className="flex-1 items-center justify-center">
          <Text className="text-lg text-gray-600">❌ ไม่พบข้อมูลงาน</Text>
        </View>
      </SafeAreaView>
    );
  }

  return (
    <SafeAreaView className="flex-1 bg-white">
      <ScrollView className="flex-1">
        <View className="bg-blue-50 px-4 pt-6 pb-8">
          <Text className="text-2xl font-bold text-gray-800 mb-2">
            {job.title}
          </Text>
          <Text className="text-lg text-gray-600 mb-4">
            🏢 {job.company}
          </Text>

          <View className="flex-row flex-wrap gap-2">
            <View className="bg-blue-100 px-4 py-2 rounded-lg">
              <Text className="text-sm text-blue-700 font-medium">
                📍 {job.location}
              </Text>
            </View>
            <View className="bg-green-100 px-4 py-2 rounded-lg">
              <Text className="text-sm text-green-700 font-medium">
                ⏰ {job.type}
              </Text>
            </View>
          </View>
        </View>

        <View className="px-4 pt-6">
          <View className="mb-6">
            <Text className="text-lg font-bold text-gray-800 mb-2">
              💰 เงินเดือน
            </Text>
            <View className="bg-orange-50 p-4 rounded-lg border border-orange-200">
              <Text className="text-base text-orange-700 font-semibold">
                {job.salary}
              </Text>
            </View>
          </View>

          <View className="mb-6">
            <Text className="text-lg font-bold text-gray-800 mb-2">
              📝 รายละเอียดงาน
            </Text>
            <Text className="text-base text-gray-700 leading-6">
              {job.description}
            </Text>
          </View>

          <View className="mb-8">
            <Text className="text-lg font-bold text-gray-800 mb-3">
              ✅ คุณสมบัติที่ต้องการ
            </Text>
            {job.requirements.map((req, index) => (
              <View key={index} className="flex-row mb-3 bg-gray-50 p-3 rounded-lg">
                <Text className="text-blue-600 mr-3 text-base">•</Text>
                <Text className="text-base text-gray-700 flex-1 leading-6">
                  {req}
                </Text>
              </View>
            ))}
          </View>
        </View>
      </ScrollView>
    </SafeAreaView>
  );
}
```

---

## <a name="step8">8. การรันโปรเจค</a>

### 8.1 Clear Cache และรันโปรเจค

```bash
npx expo start --clear
```

**สำคัญ:** ใช้ `--clear` เพื่อลบ cache ทุกครั้งที่รันครั้งแรก

### 8.2 เลือกแพลตฟอร์ม

หลังจากรันคำสั่งจะมี QR Code ปรากฏขึ้น:

- กด `i` → iOS Simulator (ต้องมี Xcode)
- กด `a` → Android Emulator (ต้องมี Android Studio)
- กด `w` → Web Browser
- **สแกน QR Code** → มือถือจริง (ต้องติดตั้ง Expo Go App)

### 8.3 ติดตั้ง Expo Go App (สำหรับมือถือจริง)

- **iOS:** [Expo Go บน App Store](https://apps.apple.com/app/expo-go/id982107779)
- **Android:** [Expo Go บน Play Store](https://play.google.com/store/apps/details?id=host.exp.exponent)

### 8.4 ทดสอบการทำงาน

1. ✅ เปิดแอปเห็นหน้าแรกแสดงรายการงาน 6 ตำแหน่ง
2. ✅ กดที่การ์ดงานใดก็ได้
3. ✅ ไปยังหน้ารายละเอียดของงานนั้น
4. ✅ กดปุ่ม Back กลับหน้าแรก
5. ✅ Tailwind CSS ทำงานถูกต้อง (มีสี, border, spacing)

---

## <a name="troubleshooting">9. การแก้ปัญหา</a>

### ⚠️ ปัญหา 1: Cannot find module 'expo-router'

**สาเหตุ:** expo-router ยังไม่ได้ติดตั้ง

**วิธีแก้:**

```bash
npx expo install expo-router react-native-safe-area-context react-native-screens expo-linking expo-constants expo-status-bar
```

---

### ⚠️ ปัญหา 2: Unable to resolve "../../../App"

**สาเหตุ:** package.json ยังไม่ได้แก้ไข

**วิธีแก้:**

1. เปิด `package.json`
2. แก้ไข `"main"`:

```json
{
  "main": "expo-router/entry"
}
```

3. **ลบไฟล์ `App.tsx`** (สำคัญมาก!)

```bash
rm App.tsx
```

4. รันใหม่

```bash
npx expo start --clear
```

---

### ⚠️ ปัญหา 3: Tailwind ไม่ทำงาน

**วิธีแก้:**

1. ตรวจสอบ `tailwind.config.js`:

```javascript
module.exports = {
  content: [
    "./app/**/*.{js,jsx,ts,tsx}",
    "./components/**/*.{js,jsx,ts,tsx}"
  ],
  theme: {
    extend: {},
  },
  plugins: [],
}
```

2. ตรวจสอบ `babel.config.js`:

```javascript
module.exports = function (api) {
  api.cache(true);
  return {
    presets: ['babel-preset-expo'],
    plugins: ['nativewind/babel'],
  };
};
```

3. **Clear cache และรันใหม่:**

```bash
npx expo start --clear
```

---

### ⚠️ ปัญหา 4: หน้าจอว่างเปล่า (White Screen)

**วิธีแก้:**

1. **ตรวจสอบ Terminal** หา Error Message
2. **ตรวจสอบโครงสร้างโฟลเดอร์:**

```
app/
├── _layout.tsx    ← ต้องมี!
├── index.tsx
└── job/
    └── [id].tsx
```

3. **Reload แอป:**
   - กด `r` ใน Terminal
   - หรือเขย่ามือถือแล้วเลือก "Reload"

4. **Clear cache:**

```bash
npx expo start --clear
```

---

### ⚠️ ปัญหา 5: Import path error

**ข้อความ Error:**
```
Unable to resolve "./data/jobs" from "app/index.tsx"
```

**วิธีแก้:**

1. ตรวจสอบ import path ให้ถูกต้อง:

```typescript
// ✅ ถูกต้อง (จาก app/index.tsx)
import { jobs } from '../data/jobs';
import JobCard from '../components/JobCard';

// ❌ ผิด
import { jobs } from './data/jobs';
import JobCard from './components/JobCard';
```

2. ตรวจสอบว่าไฟล์มีอยู่จริง:

```bash
ls data/jobs.ts
ls components/JobCard.tsx
```

---

### ⚠️ ปัญหา 6: Module Resolution Error ทั่วไป

**วิธีแก้แบบสมบูรณ์:**

```bash
# 1. ลบ node_modules และ cache
rm -rf node_modules
rm -rf .expo
rm package-lock.json

# 2. ติดตั้งใหม่
npm install

# 3. ติดตั้ง Expo Router อีกครั้ง
npx expo install expo-router react-native-safe-area-context react-native-screens expo-linking expo-constants expo-status-bar

# 4. รันพร้อม clear cache
npx expo start --clear
```

---

### 🔧 เคล็ดลับการแก้ปัญหา

1. **อ่าน Error Message ให้ดี**
   - Terminal แสดง Error ชัดเจน
   - Google Error Message พร้อมคำว่า "expo router"

2. **Clear Cache เป็นประจำ**
   ```bash
   npx expo start --clear
   ```

3. **ใช้ Console.log() Debug**
   ```typescript
   console.log('Component loaded');
   console.log('Jobs data:', jobs);
   ```

4. **Restart ทุกอย่าง**
   - กด `Ctrl+C` ใน Terminal
   - รัน `npx expo start --clear` ใหม่

---

## <a name="summary">10. สรุปและเอกสารอ้างอิง</a>

### ✅ สิ่งที่เราได้ทำ

1. ✅ สร้างโปรเจค React Native ด้วย `npx rn-new --nativewind`
2. ✅ ติดตั้ง Expo Router และ dependencies
3. ✅ ตั้งค่า package.json ให้ใช้ Expo Router
4. ✅ สร้างโครงสร้างโฟลเดอร์ `app/`, `types/`, `data/`
5. ✅ สร้าง File-based Routing
6. ✅ สร้าง Dynamic Routes สำหรับหน้ารายละเอียด
7. ✅ ใช้ TypeScript และ Nativewind (Tailwind CSS)

### 🎯 จุดสำคัญที่ต้องจำ

| ขั้นตอน | สิ่งที่ต้องทำ |
|---------|-------------|
| **1. หลังสร้างโปรเจค** | ติดตั้ง expo-router ทันที |
| **2. แก้ package.json** | เปลี่ยน main
