# 🚍 Bus Problem (Semaphore) — Driver Controls Boarding

## 🔧 Variables

```c
int Queue = N;        // عدد الركاب في الطابور
int seat_bus = M;     // عدد المقاعد

semaphore mutex = 1;  // حماية (Mutual Exclusion)
semaphore S = 0;      // تزامن (Synchronization)
```

---

## 🚍 Driver (السائق)

```c
park_bus() {

    P(mutex);   // 🔒 قفل لحماية المتغيرات

    if (Queue > 0 && seat_bus > 0) {

        V(mutex);   // 🔓 فتح

        V(S);       // ✅ يسمح لراكب واحد بالصعود
                    // ↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓↓
                    // هذا الإذن يذهب إلى الراكب المنتظر في P(S)

    } else {

        V(mutex);   // 🔓 فتح

        depart_bus(); // 🚍 مغادرة
    }
}
```

---

## 👤 Passenger (الراكب)

```c
Passenger() {

    P(mutex);   // 🔒 قفل

    if (Queue == 0) {

        V(mutex);   // 🔓 خروج

    } else {

        V(mutex);   // 🔓 فتح قبل الانتظار

        Queue--;    // 🚶‍♂️ خرج من الطابور

        P(S);       // ⛔ ينتظر هنا
                   // ↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑↑
                   // ينتظر الإذن القادم من V(S) في السائق

        Queue++;    // 🔄 إعادة توازن (modeling)
        seat_bus--; // 🪑 يركب

    }
}
```

---

## 🔗 الربط الأساسي (مهم جدًا)

```text
Driver:     V(S)   ───────────────▶   Passenger: P(S)
             (يسمح)                   (ينتظر)

V(S) يحرر P(S)
```

---

## 🧠 الخلاصة

* `P(S)` → انتظار
* `V(S)` → سماح
* الربط يتم عبر **semaphore S المشترك**

---

## 🎯 جملة للامتحان

The passenger blocks at P(S) until the driver signals using V(S), which allows the passenger to continue and board the bus.
