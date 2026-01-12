# 📱 คู่มือการสร้างแอป IT Worker ด้วย React Native + Expo Router

## เกี่ยวกับโปรเจค

แอปพลิเคชันค้นหางานในสายอาชีพ IT ที่พัฒนาด้วย:
- ⚛️ React Native
- 📘 TypeScript
- 🎨 Nativewind (Tailwind CSS)
- 🧭 Expo Router (File-based Routing)

**ความสามารถ:**
- แสดงรายการงาน IT ทั้งหมด
- ดูรายละเอียดของแต่ละตำแหน่งงาน
- Navigation แบบ File-based Routing

---

## 📋 สารบัญ

1. [การติดตั้งและสร้างโปรเจค](#step1)
2. [ทำความเข้าใจ Expo Router](#step2)
3. [โครงสร้างโปรเจค](#step3)
4. [สร้างไฟล์ Types](#step4)
5. [สร้างข้อมูลทดสอบ](#step5)
6. [สร้าง Component](#step6)
7. [สร้างหน้าจอ](#step7)
8. [การรันโปรเจค](#step8)
9. [การแก้ปัญหา](#troubleshooting)
10. [สรุปและเอกสารอ้างอิง](#summary)

---

## <a name="step1">1. การติดตั้งและสร้างโปรเจค</a>

### 1.1 สร้างโปรเจค

```bash
npx rn-new it-worker --nativewind
cd it-worker
```

คำสั่งนี้จะสร้างโปรเจคพร้อม:
- ✅ Expo Router
- ✅ TypeScript
- ✅ Nativewind (Tailwind CSS)

**ไม่ต้องติดตั้งอะไรเพิ่ม!**

**อ้างอิง:**
- [Nativewind Installation](https://www.nativewind.dev/docs/getting-started/installation)
- [Expo Router Documentation](https://docs.expo.dev/router/introduction/)

---

## <a name="step2">2. ทำความเข้าใจ Expo Router</a>

### 🎯 Expo Router คืออะไร?

Expo Router คือระบบ Navigation แบบ **File-based Routing** เหมือน Next.js

### ⭐ คุณสมบัติหลัก

#### 2.1 File-based Routing

```
app/
├── index.tsx           →  หน้าแรก (/)
├── about.tsx           →  หน้า About (/about)
└── job/
    └── [id].tsx       →  หน้ารายละเอียด (/job/1, /job/2)
```

**ข้อดี:**
- ✅ ไม่ต้องตั้งค่า Navigation
- ✅ ชื่อไฟล์ = URL path
- ✅ เข้าใจง่าย ดูแลง่าย

#### 2.2 Dynamic Routes

ใช้วงเล็บก้ามปู `[id].tsx` สำหรับ Dynamic Parameters

```typescript
// app/job/[id].tsx
import { useLocalSearchParams } from 'expo-router';

export default function JobDetail() {
  const { id } = useLocalSearchParams();
  // URL: /job/1 → id = "1"
  // URL: /job/2 → id = "2"
}
```

#### 2.3 การนำทาง

```typescript
import { Link, router } from 'expo-router';

// วิธีที่ 1: ใช้ Link
<Link href="/job/1">ดูรายละเอียด</Link>

// วิธีที่ 2: ใช้ router.push
router.push('/job/1');

// วิธีที่ 3: ส่ง params
router.push({
  pathname: '/job/[id]',
  params: { id: '1' }
});
```

**อ้างอิง:**
- [File-based Routing](https://docs.expo.dev/router/create-pages/)
- [Dynamic Routes](https://docs.expo.dev/router/reference/url-parameters/)
- [Navigation](https://docs.expo.dev/router/navigating-pages/)

---

## <a name="step3">3. โครงสร้างโปรเจค</a>

```
it-worker/
├── app/
│   ├── _layout.tsx          # Layout หลัก
│   ├── index.tsx            # หน้าแรก
│   └── job/
│       └── [id].tsx         # หน้ารายละเอียด
├── components/
│   └── JobCard.tsx          # Component การ์ด
├── data/
│   └── jobs.ts              # ข้อมูลทดสอบ
├── types/
│   └── job.ts               # TypeScript Types
├── app.json
├── package.json
└── tsconfig.json
```

---

## <a name="step4">4. สร้างไฟล์ Types</a>

### สร้างโฟลเดอร์และไฟล์

```bash
mkdir types
```

### `types/job.ts`

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

---

## <a name="step5">5. สร้างข้อมูลทดสอบ</a>

### สร้างโฟลเดอร์และไฟล์

```bash
mkdir data
```

### `data/jobs.ts`

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
      'มีประสบการณ์ใช้ Git และ GitHub'
    ],
    type: 'Full-time'
  },
  {
    id: 2,
    title: 'Backend Developer',
    company: 'Startup B',
    salary: '45,000 - 70,000 บาท',
    location: 'เชียงใหม่',
    description: 'พัฒนา API และระบบ Backend ด้วย Node.js รองรับผู้ใช้งานจำนวนมาก ออกแบบฐานข้อมูลและเขียน API ที่มีประสิทธิภาพ',
    requirements: [
      'เชี่ยวชาญ Node.js และ Express',
      'มีประสบการณ์ Database (MySQL/MongoDB)',
      'เข้าใจ RESTful API และ GraphQL',
      'มีความรู้เรื่อง Security และ Authentication'
    ],
    type: 'Full-time'
  },
  {
    id: 3,
    title: 'Mobile Developer',
    company: 'Digital Agency C',
    salary: '35,000 - 55,000 บาท',
    location: 'ระยอง',
    description: 'พัฒนาแอปมือถือด้วย React Native สำหรับ iOS และ Android ทำงานร่วมกับทีม Backend และ Designer',
    requirements: [
      'มีประสบการณ์ React Native',
      'เข้าใจ Mobile UI/UX Patterns',
      'สามารถ Deploy แอปได้ทั้ง iOS และ Android',
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
    description: 'ออกแบบ User Interface และ User Experience สำหรับเว็บและแอปมือถือ ทำ User Research และสร้าง Prototype',
    requirements: [
      'เชี่ยวชาญ Figma หรือ Adobe XD',
      'เข้าใจหลักการ Design Thinking',
      'มี Portfolio ผลงานที่หลากหลาย',
      'สามารถทำ User Research ได้'
    ],
    type: 'Full-time'
  },
  {
    id: 5,
    title: 'Data Analyst',
    company: 'Finance Corp E',
    salary: '38,000 - 58,000 บาท',
    location: 'กรุงเทพฯ',
    description: 'วิเคราะห์ข้อมูลธุรกิจและสร้าง Dashboard สำหรับผู้บริหาร ใช้ข้อมูลในการตัดสินใจทางธุรกิจ',
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
    description: 'จัดการ Infrastructure และ CI/CD Pipeline ดูแลระบบ Cloud และ Monitoring',
    requirements: [
      'มีประสบการณ์ AWS หรือ GCP',
      'เชี่ยวชาญ Docker และ Kubernetes',
      'เข้าใจ CI/CD Pipeline',
      'มีความรู้เรื่อง Linux Administration'
    ],
    type: 'Full-time'
  }
];
```

---

## <a name="step6">6. สร้าง Component</a>

### สร้างโฟลเดอร์และไฟล์

```bash
mkdir components
```

### `components/JobCard.tsx`

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
      className="bg-white p-4 rounded-xl mb-3 shadow-sm border border-gray-200 active:scale-98"
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

## <a name="step7">7. สร้างหน้าจอ</a>

### 7.1 Layout หลัก - `app/_layout.tsx`

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

### 7.2 หน้าแรก - `app/index.tsx`

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
        {/* Header */}
        <View className="mb-4">
          <Text className="text-3xl font-bold text-gray-800 mb-2">
            งาน IT
          </Text>
          <Text className="text-base text-gray-600">
            พบ <Text className="font-semibold text-blue-600">{jobs.length}</Text> ตำแหน่งงาน
          </Text>
        </View>

        {/* Job List */}
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

### 7.3 หน้ารายละเอียด - `app/job/[id].tsx`

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
        {/* Header Section */}
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

        {/* Content Section */}
        <View className="px-4 pt-6">
          {/* Salary */}
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

          {/* Description */}
          <View className="mb-6">
            <Text className="text-lg font-bold text-gray-800 mb-2">
              📝 รายละเอียดงาน
            </Text>
            <Text className="text-base text-gray-700 leading-6">
              {job.description}
            </Text>
          </View>

          {/* Requirements */}
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

### 8.1 รันโปรเจค

```bash
npx expo start
```

### 8.2 เลือกแพลตฟอร์ม

- กด `i` → iOS Simulator
- กด `a` → Android Emulator
- กด `w` → Web Browser
- สแกน QR Code → มือถือจริง (ต้องติดตั้ง Expo Go)

### 8.3 ทดสอบการทำงาน

1. ✅ เปิดแอปเห็นรายการงาน 6 ตำแหน่ง
2. ✅ กดที่การ์ดงานใดก็ได้
3. ✅ ไปยังหน้ารายละเอียดของงานนั้น
4. ✅ กดปุ่ม Back กลับหน้าแรก

---

## <a name="troubleshooting">9. การแก้ปัญหา</a>

### ปัญหา 1: Tailwind ไม่ทำงาน

**วิธีแก้:**

1. ตรวจสอบ `tailwind.config.js`

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

2. ตรวจสอบ `babel.config.js`

```javascript
module.exports = function (api) {
  api.cache(true);
  return {
    presets: ['babel-preset-expo'],
    plugins: ["nativewind/babel"],
  };
};
```

3. ลบ cache และรันใหม่

```bash
npx expo start -c
```

### ปัญหา 2: TypeScript Error

**วิธีแก้:**

ตรวจสอบ `tsconfig.json` มี config นี้

```json
{
  "extends": "expo/tsconfig.base",
  "compilerOptions": {
    "strict": true
  }
}
```

### ปัญหา 3: Navigation ไม่ทำงาน

**วิธีแก้:**

1. ตรวจสอบโครงสร้างโฟลเดอร์ `app/`
2. ตรวจสอบชื่อไฟล์ให้ถูกต้อง
3. Restart Expo Dev Server

```bash
# กด Ctrl+C แล้วรันใหม่
npx expo start
```

---

## <a name="summary">10. สรุปและเอกสารอ้างอิง</a>

### ✅ สิ่งที่เราได้ทำ

1. ✅ สร้างโปรเจค React Native ด้วย Expo Router
2. ✅ ใช้ TypeScript สำหรับความปลอดภัยของข้อมูล
3. ✅ ใช้ Nativewind (Tailwind CSS) สำหรับ Styling
4. ✅ สร้าง File-based Routing ด้วย Expo Router
5. ✅ สร้าง Dynamic Routes สำหรับหน้ารายละเอียด
6. ✅ แยก Component และ Data เป็นไฟล์ต่างหาก

### 🎯 จุดเด่นของ Expo Router

| คุณสมบัติ | ประโยชน์ |
|-----------|----------|
| **File-based Routing** | ไม่ต้องตั้งค่า Navigation ซับซ้อน |
| **Dynamic Routes** | สร้าง URL แบบ Dynamic ได้ง่าย |
| **Type Safety** | รองรับ TypeScript เต็มรูปแบบ |
| **Deep Linking** | รองรับการเปิดแอปจาก Link อัตโนมัติ |
| **SEO Friendly** | เมื่อ Export เป็น Web ได้ SEO ที่ดี |

### 📚 เอกสารอ้างอิงทั้งหมด

#### Expo Router
- [Introduction](https://docs.expo.dev/router/introduction/)
- [File-based Routing](https://docs.expo.dev/router/create-pages/)
- [Dynamic Routes](https://docs.expo.dev/router/reference/url-parameters/)
- [Navigation](https://docs.expo.dev/router/navigating-pages/)
- [Layouts](https://docs.expo.dev/router/layouts/)

#### Nativewind
- [Official Documentation](https://www.nativewind.dev/)
- [Quick Start with Expo](https://www.nativewind.dev/quick-starts/expo)
- [Tailwind CSS Docs](https://tailwindcss.com/docs)

#### React Native
- [Getting Started](https://reactnative.dev/docs/getting-started)
- [FlatList API](https://reactnative.dev/docs/flatlist)
- [TouchableOpacity](https://reactnative.dev/docs/touchableopacity)
- [SafeAreaView](https://reactnative.dev/docs/safeareaview)

#### TypeScript
- [TypeScript Handbook](https://www.typescriptlang.org/docs/handbook/intro.html)
- [React TypeScript Cheatsheet](https://react-typescript-cheatsheet.netlify.app/)

### 🚀 ขั้นตอนต่อไป

เมื่อเข้าใจพื้นฐานแล้ว คุณสามารถพัฒนาต่อได้:

1. **เพิ่มฟีเจอร์ Search**
   - ค้นหางานตามชื่อตำแหน่ง
   - ใช้ `useState` และ `filter()`

2. **เพิ่ม Filter**
   - กรองตามสถานที่
   - กรองตามช่วงเงินเดือน

3. **เพิ่มหน้า Favorites**
   - บันทึกงานที่สนใจ
   - ใช้ `AsyncStorage`

4. **เชื่อมต่อ API**
   - ดึงข้อมูลจาก Backend จริง
   - ใช้ `fetch` หรือ `axios`

5. **เพิ่ม Animation**
   - ใช้ `react-native-reanimated`
   - ทำ Transition สวยงาม

### 💡 Tips สำหรับผู้เริ่มต้น

1. **อ่าน Error Message**
   - Error message บอกปัญหาได้ตรง
   - Google ปัญหาพร้อม Error Message

2. **ใช้ Console.log()**
   - Debug ด้วย `console.log()`
   - ดูค่าตัวแปรในแต่ละขั้นตอน

3. **เริ่มจากง่ายไปยาก**
   - เริ่มจากโครงสร้างพื้นฐาน
   - ค่อยๆ เพิ่มฟีเจอร์ทีละอย่าง

4. **ศึกษา Documentation**
   - อ่าน Official Docs เป็นหลัก
   - ดูตัวอย่างโค้ดใน Docs

5. **ทดลองแก้ไข**
   - ลองเปลี่ยนสี, ขนาด, Layout
   - เรียนรู้จากการทดลอง

### 📝 Checklist การสร้างโปรเจค

- [ ] สร้างโปรเจคด้วย `npx rn-new --nativewind`
- [ ] สร้างโฟลเดอร์ `types/`, `data/`, `components/`
- [ ] สร้างไฟล์ `types/job.ts`
- [ ] สร้างไฟล์ `data/jobs.ts`
- [ ] สร้างไฟล์ `components/JobCard.tsx`
- [ ] แก้ไขไฟล์ `app/_layout.tsx`
- [ ] แก้ไขไฟล์ `app/index.tsx`
- [ ] สร้างไฟล์ `app/job/[id].tsx`
- [ ] รันโปรเจคด้วย `npx expo start`
- [ ] ทดสอบการทำงานทั้งหมด

---

## 🎉 สรุป

ยินดีด้วย! คุณได้เรียนรู้การสร้างแอป React Native ด้วย **Expo Router** ที่ทันสมัยและใช้งานง่าย

**สิ่งที่ได้เรียนรู้:**
- ✅ File-based Routing แบบ Next.js
- ✅ Dynamic Routes สำหรับหน้ารายละเอียด
- ✅ TypeScript สำหรับความปลอดภัย
- ✅ Tailwind CSS สำหรับ Styling
- ✅ Component-based Architecture

**ข้อดีของ Expo Router:**
- 🚀 รวดเร็ว ไม่ต้องตั้งค่าเยอะ
- 📱 รองรับ Deep Linking อัตโนมัติ
- 🎨 เขียน Code น้อยกว่า
- 🔒 Type-safe ด้วย TypeScript

---

**Happy Coding! 🚀**

*เอกสารนี้อัพเดทล่าสุด: 2025*
