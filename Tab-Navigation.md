# 📱 การเพิ่ม Tabs Navigation และหน้าเกี่ยวกับแอป

## 🎯 ภาพรวม

คู่มือนี้เป็นส่วนเสริมจากคู่มือหลัก **"คู่มือสร้าง IT Worker App"** โดยจะสอนวิธีเพิ่ม:
- ✅ Tabs Navigation (แบบ Instagram, Facebook)
- ✅ หน้า "เกี่ยวกับแอป"
- ✅ ไอคอนสวยงามบน Tab Bar
- ✅ เปลี่ยนหน้าได้ง่ายๆ

**คำเตือน:** ต้องทำคู่มือหลักให้เสร็จก่อน!

---

## 📋 สารบัญ

1. [ทำความเข้าใจ Tabs Navigation](#intro)
2. [ปรับโครงสร้างโปรเจค](#structure)
3. [สร้างไฟล์ Tabs Layout](#tabs-layout)
4. [ย้ายไฟล์เดิมเข้า Tabs](#move-files)
5. [สร้างหน้า About](#about-page)
6. [แก้ไข Root Layout](#root-layout)
7. [ทดสอบการทำงาน](#testing)
8. [การแก้ปัญหา](#troubleshooting)

---

## <a name="intro"></a>1. ทำความเข้าใจ Tabs Navigation

### 🎯 Tabs Navigation คืออะไร?

**Tabs Navigation** คือแถบด้านล่างที่มีไอคอนให้กดเปลี่ยนหน้า เหมือน:
- 📱 Instagram (Home, Search, Reels, Profile)
- 📘 Facebook (Home, Videos, Marketplace, Profile)
- 🎵 Spotify (Home, Search, Library)

### ⭐ โครงสร้างใน Expo Router

```
app/
├── _layout.tsx              ← Root Layout (Stack)
├── (tabs)/                  ← Group สำหรับ Tabs (ต้องมีวงเล็บ!)
│   ├── _layout.tsx         ← Tabs Layout
│   ├── index.tsx           ← หน้าแรก (งาน IT)
│   └── about.tsx           ← หน้าเกี่ยวกับแอป
└── job/
    └── [id].tsx            ← หน้ารายละเอียดงาน (Stack)
```

**อ้างอิง:** [Expo Router - Tabs](https://docs.expo.dev/router/advanced/tabs/)

---

## <a name="structure"></a>2. ปรับโครงสร้างโปรเจค

### 2.1 สร้างโฟลเดอร์ (tabs)

```bash
mkdir "app/(tabs)"
```

**สำคัญ:** ต้องมีวงเล็บ `(tabs)` - ชื่อใน `()` จะไม่แสดงใน URL

### 2.2 โครงสร้างใหม่

```
it-worker/
├── app/
│   ├── _layout.tsx          ← แก้ไขใหม่
│   ├── (tabs)/              ← สร้างใหม่
│   │   ├── _layout.tsx      ← สร้างใหม่
│   │   ├── index.tsx        ← ย้ายจาก app/index.tsx
│   │   └── about.tsx        ← สร้างใหม่
│   └── job/
│       └── [id].tsx         ← เดิม (ไม่ต้องแก้)
├── components/
│   └── JobCard.tsx          ← เดิม (ไม่ต้องแก้)
├── data/
│   └── jobs.ts              ← เดิม (ไม่ต้องแก้)
└── types/
    └── job.ts               ← เดิม (ไม่ต้องแก้)
```

---

## <a name="tabs-layout"></a>3. สร้างไฟล์ Tabs Layout

### สร้างไฟล์ `app/(tabs)/_layout.tsx`

```typescript
// app/(tabs)/_layout.tsx
import { Tabs } from 'expo-router';
import { Ionicons } from '@expo/vector-icons';

export default function TabsLayout() {
  return (
    <Tabs
      screenOptions={{
        tabBarActiveTintColor: '#3b82f6',
        tabBarInactiveTintColor: '#6b7280',
        tabBarStyle: {
          backgroundColor: '#ffffff',
          borderTopWidth: 1,
          borderTopColor: '#e5e7eb',
          height: 60,
          paddingBottom: 8,
          paddingTop: 8,
        },
        tabBarLabelStyle: {
          fontSize: 12,
          fontWeight: '600',
        },
        headerStyle: {
          backgroundColor: '#3b82f6',
        },
        headerTintColor: '#fff',
        headerTitleStyle: {
          fontWeight: 'bold',
        },
      }}
    >
      <Tabs.Screen
        name="index"
        options={{
          title: 'งาน IT',
          tabBarLabel: 'หน้าแรก',
          tabBarIcon: ({ color, size }) => (
            <Ionicons name="briefcase" size={size} color={color} />
          ),
        }}
      />
      <Tabs.Screen
        name="about"
        options={{
          title: 'เกี่ยวกับแอป',
          tabBarLabel: 'เกี่ยวกับ',
          tabBarIcon: ({ color, size }) => (
            <Ionicons name="information-circle" size={size} color={color} />
          ),
        }}
      />
    </Tabs>
  );
}
```

**คำอธิบาย:**
- `<Tabs>` สร้าง Tab Bar ด้านล่าง
- `tabBarActiveTintColor` สีเมื่อเลือก Tab
- `tabBarIcon` แสดงไอคอนจาก Ionicons
- `name="index"` ชี้ไปที่ `(tabs)/index.tsx`

---

## <a name="move-files"></a>4. ย้ายไฟล์เดิมเข้า Tabs

### 4.1 ย้ายไฟล์ index.tsx

```bash
# สำหรับ Mac/Linux
mv app/index.tsx "app/(tabs)/index.tsx"

# สำหรับ Windows (PowerShell)
Move-Item app/index.tsx "app/(tabs)/index.tsx"
```

### 4.2 แก้ไข Import Path ใน index.tsx

เปิดไฟล์ `app/(tabs)/index.tsx` และแก้ไข import:

```typescript
// app/(tabs)/index.tsx
import React from 'react';
import { View, Text, FlatList, SafeAreaView } from 'react-native';
import { router } from 'expo-router';
import JobCard from '../../components/JobCard';  // แก้จาก ../components
import { jobs } from '../../data/jobs';           // แก้จาก ../data

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

**สำคัญ:** เปลี่ยน `../` เป็น `../../` เพราะอยู่ลึกลงไป 1 ระดับ!

---

## <a name="about-page"></a>5. สร้างหน้า About

### สร้างไฟล์ `app/(tabs)/about.tsx`

```typescript
// app/(tabs)/about.tsx
import React from 'react';
import { View, Text, ScrollView, SafeAreaView } from 'react-native';
import { Ionicons } from '@expo/vector-icons';

export default function About() {
  return (
    <SafeAreaView className="flex-1 bg-gray-50">
      <ScrollView className="flex-1">
        <View className="px-4 pt-6">
          {/* Header */}
          <View className="items-center mb-8">
            <View className="bg-blue-100 w-24 h-24 rounded-full items-center justify-center mb-4">
              <Ionicons name="briefcase" size={48} color="#3b82f6" />
            </View>
            <Text className="text-3xl font-bold text-gray-800 mb-2">
              IT Worker
            </Text>
            <Text className="text-base text-gray-600">
              แอปหางานในสายอาชีพ IT
            </Text>
          </View>

          {/* Version */}
          <View className="bg-white p-4 rounded-xl mb-4 border border-gray-200">
            <View className="flex-row items-center">
              <Ionicons name="code-slash" size={24} color="#3b82f6" />
              <View className="ml-3">
                <Text className="text-sm text-gray-600">เวอร์ชัน</Text>
                <Text className="text-lg font-semibold text-gray-800">1.0.0</Text>
              </View>
            </View>
          </View>

          {/* Description */}
          <View className="bg-white p-4 rounded-xl mb-4 border border-gray-200">
            <View className="flex-row items-start mb-2">
              <Ionicons name="information-circle" size={24} color="#3b82f6" />
              <Text className="text-lg font-bold text-gray-800 ml-3">
                เกี่ยวกับแอป
              </Text>
            </View>
            <Text className="text-base text-gray-700 leading-6 mt-2">
              IT Worker เป็นแอปพลิเคชันสำหรับค้นหางานในสายอาชีพ IT 
              ที่รวบรวมตำแหน่งงานจากบริษัทชั้นนำทั่วประเทศ 
              พร้อมรายละเอียดงานและคุณสมบัติที่ต้องการ
            </Text>
          </View>

          {/* Features */}
          <View className="bg-white p-4 rounded-xl mb-4 border border-gray-200">
            <View className="flex-row items-start mb-3">
              <Ionicons name="sparkles" size={24} color="#3b82f6" />
              <Text className="text-lg font-bold text-gray-800 ml-3">
                ฟีเจอร์หลัก
              </Text>
            </View>
            
            <View className="space-y-3">
              <View className="flex-row items-start mt-2">
                <Ionicons name="checkmark-circle" size={20} color="#10b981" />
                <Text className="text-base text-gray-700 ml-3 flex-1">
                  ดูรายการงาน IT ทั้งหมด
                </Text>
              </View>
              
              <View className="flex-row items-start mt-2">
                <Ionicons name="checkmark-circle" size={20} color="#10b981" />
                <Text className="text-base text-gray-700 ml-3 flex-1">
                  ดูรายละเอียดงานแต่ละตำแหน่ง
                </Text>
              </View>
              
              <View className="flex-row items-start mt-2">
                <Ionicons name="checkmark-circle" size={20} color="#10b981" />
                <Text className="text-base text-gray-700 ml-3 flex-1">
                  ดูเงินเดือนและคุณสมบัติที่ต้องการ
                </Text>
              </View>
              
              <View className="flex-row items-start mt-2">
                <Ionicons name="checkmark-circle" size={20} color="#10b981" />
                <Text className="text-base text-gray-700 ml-3 flex-1">
                  UI/UX ที่ใช้งานง่าย สวยงาม
                </Text>
              </View>
            </View>
          </View>

          {/* Technology Stack */}
          <View className="bg-white p-4 rounded-xl mb-4 border border-gray-200">
            <View className="flex-row items-start mb-3">
              <Ionicons name="construct" size={24} color="#3b82f6" />
              <Text className="text-lg font-bold text-gray-800 ml-3">
                เทคโนโลยี
              </Text>
            </View>
            
            <View className="flex-row flex-wrap gap-2 mt-2">
              <View className="bg-blue-50 px-3 py-2 rounded-lg">
                <Text className="text-sm text-blue-700 font-medium">
                  React Native
                </Text>
              </View>
              <View className="bg-blue-50 px-3 py-2 rounded-lg">
                <Text className="text-sm text-blue-700 font-medium">
                  Expo Router
                </Text>
              </View>
              <View className="bg-blue-50 px-3 py-2 rounded-lg">
                <Text className="text-sm text-blue-700 font-medium">
                  TypeScript
                </Text>
              </View>
              <View className="bg-blue-50 px-3 py-2 rounded-lg">
                <Text className="text-sm text-blue-700 font-medium">
                  Nativewind
                </Text>
              </View>
            </View>
          </View>

          {/* Developer */}
          <View className="bg-white p-4 rounded-xl mb-8 border border-gray-200">
            <View className="flex-row items-start mb-2">
              <Ionicons name="person" size={24} color="#3b82f6" />
              <Text className="text-lg font-bold text-gray-800 ml-3">
                ผู้พัฒนา
              </Text>
            </View>
            <Text className="text-base text-gray-700 mt-2">
              พัฒนาโดย: นักศึกษาวิทยาลัยเทคนิคนครปฐม
            </Text>
            <Text className="text-sm text-gray-600 mt-1">
              สาขา: เทคโนโลยีสารสนเทศ
            </Text>
          </View>
        </View>
      </ScrollView>
    </SafeAreaView>
  );
}
```

---

## <a name="root-layout"></a>6. แก้ไข Root Layout

### แก้ไขไฟล์ `app/_layout.tsx`

```typescript
// app/_layout.tsx
import { Stack } from 'expo-router';

export default function RootLayout() {
  return (
    <Stack
      screenOptions={{
        headerShown: false, // ซ่อน header ของ Stack เพราะ Tabs จะจัดการเอง
      }}
    >
      <Stack.Screen name="(tabs)" options={{ headerShown: false }} />
      <Stack.Screen 
        name="job/[id]" 
        options={{
          headerShown: true,
          headerStyle: {
            backgroundColor: '#3b82f6',
          },
          headerTintColor: '#fff',
          headerTitleStyle: {
            fontWeight: 'bold',
          },
          title: 'รายละเอียดงาน',
        }}
      />
    </Stack>
  );
}
```

**คำอธิบาย:**
- `headerShown: false` สำหรับ Tabs (ให้ Tabs จัดการ header เอง)
- `headerShown: true` สำหรับหน้ารายละเอียดงาน (แสดงปุ่ม Back)

---

## <a name="testing"></a>7. ทดสอบการทำงาน

### 7.1 Clear Cache และรัน

```bash
npx expo start --clear
```

### 7.2 ทดสอบ

1. ✅ เห็น Tab Bar ด้านล่างแอป (2 tabs)
2. ✅ Tab "หน้าแรก" แสดงรายการงาน
3. ✅ Tab "เกี่ยวกับ" แสดงข้อมูลแอป
4. ✅ กดเปลี่ยน Tab ได้ลื่นไหล
5. ✅ กดดูรายละเอียดงานได้ปกติ
6. ✅ กด Back จากหน้ารายละเอียดกลับหน้าแรก

### 7.3 สิ่งที่ต้องเห็น

**หน้าแรก:**
- 📱 Tab Bar ด้านล่าง
- 💼 ไอคอนกระเป๋าเอกสาร (สีน้ำเงินเมื่อเลือก)
- 📋 รายการงาน IT

**หน้าเกี่ยวกับ:**
- ℹ️ ไอคอนวงกลมข้อมูล (สีน้ำเงินเมื่อเลือก)
- 📝 ข้อมูลแอป, ฟีเจอร์, เทคโนโลยี

---

## <a name="troubleshooting"></a>8. การแก้ปัญหา

### ⚠️ ปัญหา 1: Tab Bar ไม่แสดง

**สาเหตุ:** โครงสร้างไฟล์ไม่ถูกต้อง

**วิธีแก้:**

1. ตรวจสอบโครงสร้าง:

```
app/
├── _layout.tsx
├── (tabs)/              ← ต้องมีวงเล็บ!
│   ├── _layout.tsx
│   ├── index.tsx
│   └── about.tsx
└── job/
    └── [id].tsx
```

2. ตรวจสอบชื่อโฟลเดอร์:
   - ต้องเป็น `(tabs)` มีวงเล็บ
   - ไม่ใช่ `tabs` ไม่มีวงเล็บ

---

### ⚠️ ปัญหา 2: Import Error ใน index.tsx

**ข้อความ Error:**
```
Unable to resolve "../components/JobCard"
```

**วิธีแก้:**

เปลี่ยน import path ใน `app/(tabs)/index.tsx`:

```typescript
// ❌ ผิด
import JobCard from '../components/JobCard';
import { jobs } from '../data/jobs';

// ✅ ถูกต้อง
import JobCard from '../../components/JobCard';
import { jobs } from '../../data/jobs';
```

---

### ⚠️ ปัญหา 3: ไอคอนไม่แสดง

**สาเหตุ:** Ionicons ยังไม่โหลด

**วิธีแก้:**

1. รอให้แอปโหลดเสร็จสมบูรณ์
2. ถ้ายังไม่ขึ้น ติดตั้ง @expo/vector-icons:

```bash
npx expo install @expo/vector-icons
```

3. Reload แอป:

```bash
npx expo start --clear
```

---

### ⚠️ ปัญหา 4: กดดูรายละเอียดงานแล้ว Tab Bar หาย

**คำตอบ:** นี่คือพฤติกรรมปกติ!

- หน้ารายละเอียดงาน (`job/[id].tsx`) อยู่นอก `(tabs)` Group
- จึงไม่แสดง Tab Bar
- จะมีปุ่ม Back แทน

**ถ้าต้องการให้แสดง Tab Bar:**
- ต้องย้าย `job/[id].tsx` เข้าไปใน `(tabs)/` (แต่ไม่แนะนำ)

---

### ⚠️ ปัญหา 5: Tab Bar สูงเกินไป/ต่ำเกินไป

**วิธีแก้:**

แก้ไขใน `app/(tabs)/_layout.tsx`:

```typescript
tabBarStyle: {
  backgroundColor: '#ffffff',
  borderTopWidth: 1,
  borderTopColor: '#e5e7eb',
  height: 60,              // ปรับความสูง (iPhone: 70-80)
  paddingBottom: 8,        // ช่องว่างล่าง
  paddingTop: 8,           // ช่องว่างบน
},
```

---

## 9. เพิ่ม Tab ใหม่ (ตัวอย่างเพิ่มเติม)

### 9.1 เพิ่มหน้า "โปรด" (Favorites)

#### สร้างไฟล์ `app/(tabs)/favorites.tsx`

```typescript
// app/(tabs)/favorites.tsx
import React from 'react';
import { View, Text, SafeAreaView } from 'react-native';
import { Ionicons } from '@expo/vector-icons';

export default function Favorites() {
  return (
    <SafeAreaView className="flex-1 bg-gray-50">
      <View className="flex-1 items-center justify-center px-4">
        <Ionicons name="heart-outline" size={80} color="#d1d5db" />
        <Text className="text-xl font-bold text-gray-800 mt-4">
          งานที่ชอบ
        </Text>
        <Text className="text-base text-gray-600 mt-2 text-center">
          คุณยังไม่มีงานที่บันทึกไว้
        </Text>
      </View>
    </SafeAreaView>
  );
}
```

#### แก้ไข `app/(tabs)/_layout.tsx` เพิ่ม Tab

```typescript
// app/(tabs)/_layout.tsx
export default function TabsLayout() {
  return (
    <Tabs screenOptions={{...}}>
      <Tabs.Screen name="index" options={{...}} />
      
      {/* เพิ่ม Tab ใหม่ */}
      <Tabs.Screen
        name="favorites"
        options={{
          title: 'งานที่ชอบ',
          tabBarLabel: 'โปรด',
          tabBarIcon: ({ color, size }) => (
            <Ionicons name="heart" size={size} color={color} />
          ),
        }}
      />
      
      <Tabs.Screen name="about" options={{...}} />
    </Tabs>
  );
}
```

---

## 10. สรุปและเอกสารอ้างอิง

### ✅ สิ่งที่เพิ่มเติมได้

1. ✅ Tabs Navigation ด้วย `<Tabs>`
2. ✅ หน้า "เกี่ยวกับแอป" พร้อมข้อมูลครบถ้วน
3. ✅ ไอคอนสวยงามจาก Ionicons
4. ✅ Layout Group ด้วย `(tabs)`
5. ✅ Navigation ระหว่าง Tabs และ Stack

### 🎯 โครงสร้างสุดท้าย

```
app/
├── _layout.tsx              ← Root Layout
├── (tabs)/                  ← Tab Group
│   ├── _layout.tsx         ← Tabs Layout
│   ├── index.tsx           ← หน้าแรก (งาน IT)
│   └── about.tsx           ← หน้าเกี่ยวกับแอป
└── job/
    └── [id].tsx            ← หน้ารายละเอียดงาน
```

### 📚 เอกสารอ้างอิง

#### Expo Router
- [Tabs Navigation](https://docs.expo.dev/router/advanced/tabs/)
- [Layout Groups](https://docs.expo.dev/router/advanced/layouts/)
- [Stack + Tabs](https://docs.expo.dev/router/advanced/stack/)

#### Expo Icons
- [Ionicons Directory](https://icons.expo.fyi/Index)
- [@expo/vector-icons](https://docs.expo.dev/guides/icons/)

### 💡 Tips

1. **ลำดับ Tabs:** ใน `_layout.tsx` ลำดับที่เขียนคือลำดับที่แสดง
2. **ซ่อน Tab:** ใช้ `href: null` ถ้าไม่ต้องการแสดงใน Tab Bar
3. **Badge:** สามารถเพิ่ม `tabBarBadge` เพื่อแสดงจำนวนแจ้งเตือน
4. **Custom Tab Bar:** สามารถสร้าง Tab Bar แบบ Custom ได้

---

## 🎉 สำเร็จ!

ตอนนี้แอป IT Worker มี:
- ✅ หน้าแรกแสดงรายการงาน
- ✅ หน้าเกี่ยวกับแอป
- ✅ Tab Navigation ใช้งานง่าย
- ✅ หน้ารายละเอียดงานแบบ Stack

**Happy Coding! 🚀**
