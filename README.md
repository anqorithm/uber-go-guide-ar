<div dir="rtl">

# دليل أسلوب Uber لـ Go

- [المقدمة](#المقدمة)
- [الإرشادات](#الإرشادات)
  - [مؤشرات للواجهات (Interfaces)](#مؤشرات-للواجهات-interfaces)
  - [التحقق من امتثال الواجهة](#التحقق-من-امتثال-الواجهة)
  - [المستقبلات والواجهات](#المستقبلات-والواجهات)
  - [تعتبر قيمة Mutex الصفرية صالحة](#تعتبر-قيمة-mutex-الصفرية-صالحة)
  - [نسخ Slices و Maps عند الحدود](#نسخ-slices-و-maps-عند-الحدود)
  - [استخدام Defer للتنظيف](#استخدام-defer-للتنظيف)
  - [حجم القناة (Channel) واحد أو لا شيء](#حجم-القناة-channel-واحد-أو-لا-شيء)
  - [بدء التعدادات (Enums) عند الرقم واحد](#بدء-التعدادات-enums-عند-الرقم-واحد)
  - [استخدام "time" للتعامل مع الوقت](#استخدام-time-للتعامل-مع-الوقت)
  - [الأخطاء](#الأخطاء)
    - [أنواع الأخطاء](#أنواع-الأخطاء)
    - [تغليف الأخطاء](#تغليف-الأخطاء)
    - [تسمية الأخطاء](#تسمية-الأخطاء)
    - [التعامل مع الأخطاء مرة واحدة](#التعامل-مع-الأخطاء-مرة-واحدة)
  - [التعامل مع فشل تأكيد النوع](#التعامل-مع-فشل-تأكيد-النوع)
  - [لا تستخدم Panic](#لا-تستخدم-panic)
  - [استخدم go.uber.org/atomic](#استخدم-gouberorgatomic)
  - [تجنب المتغيرات العامة القابلة للتغيير](#تجنب-المتغيرات-العامة-القابلة-للتغيير)
  - [تجنب تضمين الأنواع في الهياكل العامة](#تجنب-تضمين-الأنواع-في-الهياكل-العامة)
  - [تجنب استخدام الأسماء المدمجة](#تجنب-استخدام-الأسماء-المدمجة)
  - [تجنب `init()`](#تجنب-init)
  - [الخروج في الدالة Main](#الخروج-في-الدالة-main)
    - [الخروج مرة واحدة](#الخروج-مرة-واحدة)
  - [استخدام علامات الحقل في الهياكل المحولة](#استخدام-علامات-الحقل-في-الهياكل-المحولة)
  - [لا تطلق goroutines دون متابعة](#لا-تطلق-goroutines-دون-متابعة)
    - [انتظر خروج goroutines](#انتظر-خروج-goroutines)
    - [لا تضع goroutines في `init()`](#لا-تضع-goroutines-في-init)
- [الأداء](#الأداء)
  - [تفضيل strconv على fmt](#تفضيل-strconv-على-fmt)
  - [تجنب التحويلات المتكررة من النص إلى البايت](#تجنب-التحويلات-المتكررة-من-النص-إلى-البايت)
  - [تفضيل تحديد سعة الحاويات](#تفضيل-تحديد-سعة-الحاويات)
- [الأسلوب](#الأسلوب)
  - [تجنب الأسطر الطويلة جدًا](#تجنب-الأسطر-الطويلة-جدًا)
  - [كن متسقًا](#كن-متسقًا)
  - [تجميع التصريحات المتشابهة](#تجميع-التصريحات-المتشابهة)
  - [ترتيب مجموعات الاستيراد](#ترتيب-مجموعات-الاستيراد)
  - [أسماء الحزم](#أسماء-الحزم)
  - [أسماء الدوال](#أسماء-الدوال)
  - [استخدام الأسماء المستعارة في الاستيراد](#استخدام-الأسماء-المستعارة-في-الاستيراد)
  - [تجميع وترتيب الدوال](#تجميع-وترتيب-الدوال)
  - [تقليل التداخل](#تقليل-التداخل)
  - [تجنب استخدام Else غير الضروري](#تجنب-استخدام-else-غير-الضروري)
  - [تصريحات المتغيرات على المستوى العالي](#تصريحات-المتغيرات-على-المستوى-العالي)
  - [اضافة بادئة _ للمتغيرات العامة غير المصدرة](#اضافة-بادئة-_-للمتغيرات-العامة-غير-المصدرة)
  - [التضمين في الهياكل](#التضمين-في-الهياكل)
  - [تصريحات المتغيرات المحلية](#تصريحات-المتغيرات-المحلية)
  - [nil هي قيمة صالحة لـ slice](#nil-هي-قيمة-صالحة-لـ-slice)
  - [تقليل نطاق المتغيرات](#تقليل-نطاق-المتغيرات)
  - [تجنب المعاملات المجردة](#تجنب-المعاملات-المجردة)
  - [استخدام محارف النصوص الخام لتجنب الهروب](#استخدام-محارف-النصوص-الخام-لتجنب-الهروب)
  - [تهيئة الهياكل](#تهيئة-الهياكل)
    - [استخدام أسماء الحقول لتهيئة الهياكل](#استخدام-أسماء-الحقول-لتهيئة-الهياكل)
    - [حذف حقول القيمة الصفرية في الهياكل](#حذف-حقول-القيمة-الصفرية-في-الهياكل)
    - [استخدام `var` للهياكل ذات القيمة الصفرية](#استخدام-var-للهياكل-ذات-القيمة-الصفرية)
    - [تهيئة مراجع الهياكل](#تهيئة-مراجع-الهياكل)
  - [تهيئة Maps](#تهيئة-maps)
  - [سلاسل التنسيق خارج Printf](#سلاسل-التنسيق-خارج-printf)
  - [تسمية دوال Printf](#تسمية-دوال-printf)
- [الأنماط](#الأنماط)
  - [جداول الاختبار](#جداول-الاختبار)
  - [الخيارات الوظيفية](#الخيارات-الوظيفية)
- [التدقيق اللغوي](#التدقيق-اللغوي)

## المقدمة

الأساليب هي القواعد التي تحكم كودنا. مصطلح "الأسلوب" قد يكون مضللاً بعض الشيء، لأن هذه القواعد تغطي أكثر بكثير من مجرد تنسيق ملف المصدر - فـ gofmt يتعامل مع ذلك بالنسبة لنا.

الهدف من هذا الدليل هو إدارة هذا التعقيد من خلال وصف بالتفصيل ما يجب وما لا يجب فعله عند كتابة كود Go في Uber. توجد هذه القواعد للحفاظ على قاعدة الشفرة قابلة للإدارة مع السماح للمهندسين باستخدام ميزات لغة Go بشكل إنتاجي.

تم إنشاء هذا الدليل في الأصل بواسطة [Prashant Varanasi](https://github.com/prashantv) و [Simon Newton](https://github.com/nomis52) كطريقة لمساعدة بعض الزملاء على اللحاق باستخدام Go. على مر السنين، تم تعديله بناءً على ملاحظات الآخرين.

تصف هذه الوثيقة القواعد الأسلوبية في كود Go التي نتبعها في Uber. الكثير من هذه هي إرشادات عامة لـ Go، في حين أن البعض الآخر يستند إلى موارد خارجية:

1. [Effective Go](https://go.dev/doc/effective_go)
2. [Go Common Mistakes](https://go.dev/wiki/CommonMistakes)
3. [Go Code Review Comments](https://go.dev/wiki/CodeReviewComments)

نهدف إلى أن تكون عينات الكود دقيقة للإصدارين الفرعيين الأخيرين من [إصدارات](https://go.dev/doc/devel/release) Go.

يجب أن يكون كل الكود خاليًا من الأخطاء عند تشغيله من خلال `golint` و `go vet`. نوصي بإعداد محرر النصوص الخاص بك لـ:

- تشغيل `goimports` عند الحفظ
- تشغيل `golint` و `go vet` للتحقق من الأخطاء

يمكنك العثور على معلومات حول دعم محرر النصوص لأدوات Go هنا:
https://go.dev/wiki/IDEsAndTextEditorPlugins

## الإرشادات

### مؤشرات للواجهات (Interfaces)

نادرًا ما تحتاج إلى مؤشر إلى واجهة. يجب عليك تمرير الواجهات كقيم - البيانات الأساسية يمكن أن تظل مؤشرًا.

الواجهة هي حقلين:

1. مؤشر إلى بعض المعلومات الخاصة بالنوع. يمكنك التفكير في هذا على أنه "النوع".
2. مؤشر البيانات. إذا كانت البيانات المخزنة مؤشرًا، فيتم تخزينها مباشرة. إذا كانت البيانات المخزنة قيمة، فسيتم تخزين مؤشر للقيمة.

إذا كنت تريد أن تقوم طرق الواجهة بتعديل البيانات الأساسية، فيجب عليك استخدام مؤشر.

### التحقق من امتثال الواجهة

تحقق من امتثال الواجهة في وقت التجميع حيثما كان ذلك مناسبًا. وهذا يشمل:

- الأنواع المصدرة التي يجب أن تنفذ واجهات محددة كجزء من عقد API الخاص بها
- الأنواع المصدرة أو غير المصدرة التي تعد جزءًا من مجموعة من الأنواع التي تنفذ نفس الواجهة
- حالات أخرى حيث سيؤدي انتهاك واجهة إلى تعطيل المستخدمين

<table>
<thead><tr><th>سيء</th><th>جيد</th></tr></thead>
<tbody>
<tr><td>

```go
type Handler struct {
  // ...
}



func (h *Handler) ServeHTTP(
  w http.ResponseWriter,
  r *http.Request,
) {
  ...
}
```

</td><td>

```go
type Handler struct {
  // ...
}

var _ http.Handler = (*Handler)(nil)

func (h *Handler) ServeHTTP(
  w http.ResponseWriter,
  r *http.Request,
) {
  // ...
}
```

</td></tr>
</tbody></table>

البيان `var _ http.Handler = (*Handler)(nil)` سيفشل في التجميع إذا توقف `*Handler` عن مطابقة واجهة `http.Handler`.

يجب أن يكون الجانب الأيمن من التعيين هو القيمة الصفرية للنوع المؤكد. وهذا هو `nil` لأنواع المؤشرات (مثل `*Handler`)، والشرائح، والخرائط، وهيكل فارغ لأنواع الهياكل.

```go
type LogHandler struct {
  h   http.Handler
  log *zap.Logger
}

var _ http.Handler = LogHandler{}

func (h LogHandler) ServeHTTP(
  w http.ResponseWriter,
  r *http.Request,
) {
  // ...
}
```

### المستقبلات والواجهات

يمكن استدعاء الدوال ذات مستقبلات القيمة على المؤشرات وكذلك القيم. الدوال ذات مستقبلات المؤشر يمكن استدعاؤها فقط على المؤشرات أو [القيم التي يمكن عنونتها](https://go.dev/ref/spec#Method_values).

على سبيل المثال،

```go
type S struct {
  data string
}

func (s S) Read() string {
  return s.data
}

func (s *S) Write(str string) {
  s.data = str
}

// لا يمكننا الحصول على مؤشرات للقيم المخزنة في maps، لأنها ليست
// قيم قابلة للعنونة.
sVals := map[int]S{1: {"A"}}

// يمكننا استدعاء Read على القيم المخزنة في الخريطة لأن Read
// لديها مستقبل قيمة، والذي لا يتطلب أن تكون القيمة
// قابلة للعنونة.
sVals[1].Read()

// لا يمكننا استدعاء Write على القيم المخزنة في الخريطة لأن Write
// لديها مستقبل مؤشر، ومن غير الممكن الحصول على مؤشر
// لقيمة مخزنة في خريطة.
//
//  sVals[1].Write("test")

sPtrs := map[int]*S{1: {"A"}}

// يمكنك استدعاء كل من Read و Write إذا كانت الخريطة تخزن مؤشرات،
// لأن المؤشرات قابلة للعنونة بشكل جوهري.
sPtrs[1].Read()
sPtrs[1].Write("test")
```

وبالمثل، يمكن إرضاء واجهة بواسطة مؤشر، حتى إذا كانت الطريقة لها مستقبل قيمة.

```go
type F interface {
  f()
}

type S1 struct{}

func (s S1) f() {}

type S2 struct{}

func (s *S2) f() {}

s1Val := S1{}
s1Ptr := &S1{}
s2Val := S2{}
s2Ptr := &S2{}

var i F
i = s1Val
i = s1Ptr
i = s2Ptr

// ما يلي لا يُجمَّع، لأن s2Val قيمة، ولا يوجد مستقبل قيمة لـ f.
//   i = s2Val
```

يحتوي Effective Go على مقال جيد حول [المؤشرات مقابل القيم](https://go.dev/doc/effective_go#pointers_vs_values).

### تعتبر قيمة Mutex الصفرية صالحة

القيمة الصفرية لـ `sync.Mutex` و `sync.RWMutex` صالحة، لذا نادرًا ما تحتاج إلى مؤشر إلى mutex.

<table>
<thead><tr><th>سيء</th><th>جيد</th></tr></thead>
<tbody>
<tr><td>

```go
mu := new(sync.Mutex)
mu.Lock()
```

</td><td>

```go
var mu sync.Mutex
mu.Lock()
```

</td></tr>
</tbody></table>

إذا كنت تستخدم هيكلًا بواسطة مؤشر، فيجب أن يكون mutex حقلًا غير مؤشر عليه. لا تقم بتضمين mutex في الهيكل، حتى إذا كان الهيكل غير مصدر.

<table>
<thead><tr><th>سيء</th><th>جيد</th></tr></thead>
<tbody>
<tr><td>

```go
type SMap struct {
  sync.Mutex

  data map[string]string
}

func NewSMap() *SMap {
  return &SMap{
    data: make(map[string]string),
  }
}

func (m *SMap) Get(k string) string {
  m.Lock()
  defer m.Unlock()

  return m.data[k]
}
```

</td><td>

```go
type SMap struct {
  mu sync.Mutex

  data map[string]string
}

func NewSMap() *SMap {
  return &SMap{
    data: make(map[string]string),
  }
}

func (m *SMap) Get(k string) string {
  m.mu.Lock()
  defer m.mu.Unlock()

  return m.data[k]
}
```

</td></tr>

<tr><td>

حقل `Mutex`، وطرق `Lock` و `Unlock` هي جزء غير مقصود من API المصدرة لـ `SMap`.

</td><td>

mutex وطرقه هي تفاصيل تنفيذ لـ `SMap` مخفية عن الذين يستدعونها.

</td></tr>
</tbody></table>

### نسخ Slices و Maps عند الحدود

تحتوي Slices و Maps على مؤشرات للبيانات الأساسية، لذا كن حذرًا من السيناريوهات التي تحتاج فيها إلى نسخها.

#### استلام Slices و Maps

ضع في اعتبارك أن المستخدمين يمكنهم تعديل خريطة أو شريحة استلمتها كوسيطة إذا قمت بتخزين مرجع لها.

<table>
<thead><tr><th>سيء</th> <th>جيد</th></tr></thead>
<tbody>
<tr>
<td>

```go
func (d *Driver) SetTrips(trips []Trip) {
  d.trips = trips
}

trips := ...
d1.SetTrips(trips)

// هل كنت تقصد تعديل d1.trips؟
trips[0] = ...
```

</td>
<td>

```go
func (d *Driver) SetTrips(trips []Trip) {
  d.trips = make([]Trip, len(trips))
  copy(d.trips, trips)
}

trips := ...
d1.SetTrips(trips)

// يمكننا الآن تعديل trips[0] دون التأثير على d1.trips.
trips[0] = ...
```

</td>
</tr>

</tbody>
</table>

#### إرجاع Slices و Maps

وبالمثل، كن حذرًا من تعديلات المستخدم للخرائط أو الشرائح التي تكشف عن الحالة الداخلية.

<table>
<thead><tr><th>سيء</th><th>جيد</th></tr></thead>
<tbody>
<tr><td>

```go
type Stats struct {
  mu sync.Mutex
  counters map[string]int
}

// Snapshot يعيد الإحصاءات الحالية.
func (s *Stats) Snapshot() map[string]int {
  s.mu.Lock()
  defer s.mu.Unlock()

  return s.counters
}

// لم يعد لقطة محمية بواسطة mutex، لذا فإن أي
// وصول إلى اللقطة يخضع لسباقات البيانات.
snapshot := stats.Snapshot()
```

</td><td>

```go
type Stats struct {
  mu sync.Mutex
  counters map[string]int
}

func (s *Stats) Snapshot() map[string]int {
  s.mu.Lock()
  defer s.mu.Unlock()

  result := make(map[string]int, len(s.counters))
  for k, v := range s.counters {
    result[k] = v
  }
  return result
}

// Snapshot هي الآن نسخة.
snapshot := stats.Snapshot()
```

</td></tr>
</tbody></table>

### استخدام Defer للتنظيف

استخدم defer لتنظيف الموارد مثل الملفات والأقفال.

<table>
<thead><tr><th>سيء</th><th>جيد</th></tr></thead>
<tbody>
<tr><td>

```go
p.Lock()
if p.count < 10 {
  p.Unlock()
  return p.count
}

p.count++
newCount := p.count
p.Unlock()

return newCount

// من السهل نسيان فتح الأقفال بسبب العودة المتعددة
```

</td><td>

```go
p.Lock()
defer p.Unlock()

if p.count < 10 {
  return p.count
}

p.count++
return p.count

// أكثر سهولة للقراءة
```

</td></tr>
</tbody></table>

لدى Defer نفقات عامة صغيرة جدًا ويجب تجنبها فقط إذا كنت تستطيع إثبات أن وقت تنفيذ الدالة الخاصة بك هو بترتيب النانوثانية. الفوز في القراءة من خلال استخدام defers يستحق التكلفة الضئيلة لاستخدامها. هذا صحيح بشكل خاص للطرق الأكبر التي لديها أكثر من مجرد وصول للذاكرة البسيطة، حيث تكون الحسابات الأخرى أكثر أهمية من `defer`.

### حجم القناة (Channel) واحد أو لا شيء

يجب أن يكون للقنوات عادةً حجم واحد أو غير مخزنة. بشكل افتراضي، تكون القنوات غير مخزنة ولها حجم صفر. يجب أن يخضع أي حجم آخر لمستوى عالٍ من التدقيق. ضع في اعتبارك كيفية تحديد الحجم، وما الذي يمنع القناة من الامتلاء تحت الحمل وحظر الكتاب، وماذا يحدث عندما يحدث هذا.

<table>
<thead><tr><th>سيء</th><th>جيد</th></tr></thead>
<tbody>
<tr><td>

```go
// يجب أن يكون كافياً لأي شخص!
c := make(chan int, 64)
```

</td><td>

```go
// حجم واحد
c := make(chan int, 1) // أو
// قناة غير مخزنة، حجم صفر
c := make(chan int)
```

</td></tr>
</tbody></table>

### بدء التعدادات (Enums) عند الرقم واحد

الطريقة القياسية لإدخال التعدادات في Go هي إعلان نوع مخصص ومجموعة `const` مع `iota`. نظرًا لأن المتغيرات لها قيمة افتراضية 0، يجب عليك عادةً بدء التعدادات الخاصة بك بقيمة غير صفرية.

<table>
<thead><tr><th>سيء</th><th>جيد</th></tr></thead>
<tbody>
<tr><td>

```go
type Operation int

const (
  Add Operation = iota
  Subtract
  Multiply
)

// Add=0, Subtract=1, Multiply=2
```

</td><td>

```go
type Operation int

const (
  Add Operation = iota + 1
  Subtract
  Multiply
)

// Add=1, Subtract=2, Multiply=3
```

</td></tr>
</tbody></table>

هناك حالات حيث يكون استخدام القيمة الصفرية منطقيًا، على سبيل المثال عندما تكون حالة القيمة الصفرية هي السلوك الافتراضي المرغوب.

```go
type LogOutput int

const (
  LogToStdout LogOutput = iota
  LogToFile
  LogToRemote
)

// LogToStdout=0, LogToFile=1, LogToRemote=2
```

### استخدام "time" للتعامل مع الوقت

الوقت معقد. الافتراضات الخاطئة التي غالبًا ما يتم إجراؤها حول الوقت تشمل ما يلي.

1. يوم واحد يحتوي على 24 ساعة
2. ساعة واحدة تحتوي على 60 دقيقة
3. أسبوع واحد يحتوي على 7 أيام
4. سنة واحدة تحتوي على 365 يومًا
5. [والكثير غير ذلك](https://infiniteundo.com/post/25326999628/falsehoods-programmers-believe-about-time)

على سبيل المثال، *1* يعني أن إضافة 24 ساعة إلى لحظة زمنية لن تنتج دائمًا يومًا تقويميًا جديدًا.

لذلك، استخدم دائمًا حزمة [`"time"`](https://pkg.go.dev/time) عند التعامل مع الوقت لأنها تساعد في التعامل مع هذه الافتراضات الخاطئة بطريقة أكثر أمانًا ودقة.

#### استخدم `time.Time` للحظات الزمنية

استخدم [`time.Time`](https://pkg.go.dev/time#Time) عند التعامل مع لحظات الوقت، واستخدم الطرق الموجودة في `time.Time` عند مقارنة الوقت أو إضافته أو طرحه.

<table>
<thead><tr><th>سيء</th><th>جيد</th></tr></thead>
<tbody>
<tr><td>

```go
func isActive(now, start, stop int) bool {
  return start <= now && now < stop
}
```

</td><td>

```go
func isActive(now, start, stop time.Time) bool {
  return (start.Before(now) || start.Equal(now)) && now.Before(stop)
}
```

</td></tr>
</tbody></table>

#### استخدم `time.Duration` لفترات الوقت

استخدم [`time.Duration`](https://pkg.go.dev/time#Duration) عند التعامل مع فترات الوقت.

<table>
<thead><tr><th>سيء</th><th>جيد</th></tr></thead>
<tbody>
<tr><td>

```go
func poll(delay int) {
  for {
    // ...
    time.Sleep(time.Duration(delay) * time.Millisecond)
  }
}

poll(10) // هل كانت ثوانٍ أم مللي ثانية؟
```

</td><td>

```go
func poll(delay time.Duration) {
  for {
    // ...
    time.Sleep(delay)
  }
}

poll(10*time.Second)
```

</td></tr>
</tbody></table>

بالعودة إلى مثال إضافة 24 ساعة إلى لحظة زمنية، تعتمد الطريقة التي نستخدمها لإضافة الوقت على القصد. إذا أردنا نفس الوقت من اليوم، ولكن في اليوم التقويمي التالي، يجب أن نستخدم [`Time.AddDate`](https://pkg.go.dev/time#Time.AddDate). ومع ذلك، إذا أردنا لحظة زمنية مضمونة تكون بعد 24 ساعة من الوقت السابق، يجب أن نستخدم [`Time.Add`](https://pkg.go.dev/time#Time.Add).

```go
newDay := t.AddDate(0 /* سنوات */, 0 /* شهور */, 1 /* أيام */)
maybeNewDay := t.Add(24 * time.Hour)
```

#### استخدم `time.Time` و `time.Duration` مع الأنظمة الخارجية

استخدم `time.Duration` و `time.Time` في التفاعلات مع الأنظمة الخارجية عندما يكون ذلك ممكنًا. على سبيل المثال:

- خيارات سطر الأوامر: يدعم [`flag`](https://pkg.go.dev/flag) `time.Duration` عبر [`time.ParseDuration`](https://pkg.go.dev/time#ParseDuration)
- JSON: يدعم [`encoding/json`](https://pkg.go.dev/encoding/json) ترميز `time.Time` كسلسلة [RFC 3339](https://tools.ietf.org/html/rfc3339) عبر طريقة [`UnmarshalJSON`](https://pkg.go.dev/time#Time.UnmarshalJSON)
- SQL: يدعم [`database/sql`](https://pkg.go.dev/database/sql) تحويل أعمدة `DATETIME` أو `TIMESTAMP` إلى `time.Time` والعكس إذا كان برنامج التشغيل الأساسي يدعم ذلك
- YAML: يدعم [`gopkg.in/yaml.v2`](https://pkg.go.dev/gopkg.in/yaml.v2) `time.Time` كسلسلة [RFC 3339](https://tools.ietf.org/html/rfc3339)، و `time.Duration` عبر [`time.ParseDuration`](https://pkg.go.dev/time#ParseDuration).

عندما لا يكون من الممكن استخدام `time.Duration` في هذه التفاعلات، استخدم `int` أو `float64` وقم بتضمين الوحدة في اسم الحقل.

على سبيل المثال، نظرًا لأن `encoding/json` لا يدعم `time.Duration`، يتم تضمين الوحدة في اسم الحقل.

<table>
<thead><tr><th>سيء</th><th>جيد</th></tr></thead>
<tbody>
<tr><td>

```go
// {"interval": 2}
type Config struct {
  Interval int `json:"interval"`
}
```

</td><td>

```go
// {"intervalMillis": 2000}
type Config struct {
  IntervalMillis int `json:"intervalMillis"`
}
```

</td></tr>
</tbody></table>

عندما لا يكون من الممكن استخدام `time.Time` في هذه التفاعلات، ما لم يتم الاتفاق على بديل، استخدم `string` وقم بتنسيق الطوابع الزمنية كما هو محدد في [RFC 3339](https://tools.ietf.org/html/rfc3339). يتم استخدام هذا التنسيق بشكل افتراضي بواسطة [`Time.UnmarshalText`](https://pkg.go.dev/time#Time.UnmarshalText) وهو متاح للاستخدام في `Time.Format` و `time.Parse` عبر [`time.RFC3339`](https://pkg.go.dev/time#RFC3339).

على الرغم من أن هذا لا يميل إلى أن يكون مشكلة في الممارسة العملية، ضع في اعتبارك أن حزمة `"time"` لا تدعم تحليل الطوابع الزمنية مع ثواني كبيسة ([8728](https://github.com/golang/go/issues/8728))، ولا تأخذ في الاعتبار الثواني الكبيسة في الحسابات ([15190](https://github.com/golang/go/issues/15190)). إذا قارنت بين لحظتين من الوقت، فإن الفرق لن يشمل الثواني الكبيسة التي قد تكون حدثت بين هاتين اللحظتين.

### الأخطاء

#### أنواع الأخطاء

هناك عدة خيارات لإعلان الأخطاء.
فكر في ما يلي قبل اختيار الخيار الأنسب لحالة الاستخدام الخاصة بك.

- هل يحتاج المستدعي إلى مطابقة الخطأ حتى يتمكن من معالجته؟
  إذا كان الجواب نعم، فيجب علينا دعم الدوال [`errors.Is`](https://pkg.go.dev/errors#Is) أو [`errors.As`](https://pkg.go.dev/errors#As)
  من خلال إعلان متغير خطأ على المستوى الأعلى أو نوع مخصص.
- هل رسالة الخطأ عبارة عن سلسلة ثابتة،
  أم أنها سلسلة ديناميكية تتطلب معلومات سياقية؟
  بالنسبة للأول، يمكننا استخدام [`errors.New`](https://pkg.go.dev/errors#New)، ولكن بالنسبة للأخير يجب
  استخدام [`fmt.Errorf`](https://pkg.go.dev/fmt#Errorf) أو نوع خطأ مخصص.
- هل نقوم بنشر خطأ جديد تم إرجاعه بواسطة دالة تابعة؟
  إذا كان الأمر كذلك، راجع [قسم تغليف الأخطاء](#تغليف-الأخطاء).

| مطابقة الخطأ؟ | رسالة الخطأ | التوجيه                                                             |
|--------------|------------|-------------------------------------------------------------------|
| لا            | ثابتة      | [`errors.New`](https://pkg.go.dev/errors#New)                      |
| لا            | ديناميكية  | [`fmt.Errorf`](https://pkg.go.dev/fmt#Errorf)                      |
| نعم           | ثابتة      | متغير `var` على المستوى الأعلى مع [`errors.New`](https://pkg.go.dev/errors#New) |
| نعم           | ديناميكية  | نوع `error` مخصص                                                    |

على سبيل المثال،
استخدم [`errors.New`](https://pkg.go.dev/errors#New) لخطأ بسلسلة ثابتة.
قم بتصدير هذا الخطأ كمتغير لدعم مطابقته مع `errors.Is`
إذا كان المستدعي بحاجة إلى مطابقة هذا الخطأ ومعالجته.

<table>
<thead><tr><th>بدون مطابقة خطأ</th><th>مع مطابقة خطأ</th></tr></thead>
<tbody>
<tr><td>

```go
// package foo

func Open() error {
  return errors.New("could not open")
}

// package bar

if err := foo.Open(); err != nil {
  // لا يمكن معالجة الخطأ.
  panic("unknown error")
}
```

</td><td>

```go
// package foo

var ErrCouldNotOpen = errors.New("could not open")

func Open() error {
  return ErrCouldNotOpen
}

// package bar

if err := foo.Open(); err != nil {
  if errors.Is(err, foo.ErrCouldNotOpen) {
    // معالجة الخطأ
  } else {
    panic("unknown error")
  }
}
```

</td></tr>
</tbody></table>

بالنسبة للخطأ ذي السلسلة الديناميكية،
استخدم [`fmt.Errorf`](https://pkg.go.dev/fmt#Errorf) إذا لم يحتج المستدعي إلى مطابقته،
ونوع `error` مخصص إذا احتاج المستدعي إلى مطابقته.

<table>
<thead><tr><th>بدون مطابقة خطأ</th><th>مع مطابقة خطأ</th></tr></thead>
<tbody>
<tr><td>

```go
// package foo

func Open(file string) error {
  return fmt.Errorf("file %q not found", file)
}

// package bar

if err := foo.Open("testfile.txt"); err != nil {
  // لا يمكن معالجة الخطأ.
  panic("unknown error")
}
```

</td><td>

```go
// package foo

type NotFoundError struct {
  File string
}

func (e *NotFoundError) Error() string {
  return fmt.Sprintf("file %q not found", e.File)
}

func Open(file string) error {
  return &NotFoundError{File: file}
}


// package bar

if err := foo.Open("testfile.txt"); err != nil {
  var notFound *NotFoundError
  if errors.As(err, &notFound) {
    // معالجة الخطأ
  } else {
    panic("unknown error")
  }
}
```

</td></tr>
</tbody></table>

لاحظ أنه إذا قمت بتصدير متغيرات أو أنواع الخطأ من حزمة،
فستصبح جزءًا من واجهة API العامة للحزمة.

#### تغليف الأخطاء

هناك ثلاثة خيارات رئيسية لنشر الأخطاء إذا فشل استدعاء:

- إرجاع الخطأ الأصلي كما هو
- إضافة سياق باستخدام `fmt.Errorf` و الفعل `%w`
- إضافة سياق باستخدام `fmt.Errorf` و الفعل `%v`

أعد الخطأ الأصلي كما هو إذا لم يكن هناك سياق إضافي لإضافته.
هذا يحافظ على نوع الخطأ الأصلي والرسالة.
هذا مناسب جيدًا للحالات التي تحتوي فيها رسالة الخطأ الأساسية
على معلومات كافية لتتبع مكان ظهورها.

وإلا، أضف سياقًا إلى رسالة الخطأ حيثما أمكن
حتى تتمكن بدلاً من خطأ غامض مثل "الاتصال مرفوض"،
من الحصول على أخطاء أكثر فائدة مثل "استدعاء الخدمة foo: الاتصال مرفوض".

استخدم `fmt.Errorf` لإضافة سياق إلى أخطائك،
واختر بين الأفعال `%w` أو `%v`
بناءً على ما إذا كان المستدعي يجب أن يكون قادرًا على
مطابقة واستخراج السبب الأساسي.

- استخدم `%w` إذا كان من المفترض أن يكون للمستدعي حق الوصول إلى الخطأ الأساسي.
  هذا هو الإعداد الافتراضي الجيد لمعظم الأخطاء المغلفة،
  ولكن كن على دراية بأن المستدعين قد يبدأون في الاعتماد على هذا السلوك.
  لذلك بالنسبة للحالات التي يكون فيها الخطأ المغلف هو متغير `var` معروف أو نوع،
  قم بتوثيقه واختباره كجزء من عقد الدالة الخاصة بك.
- استخدم `%v` لحجب الخطأ الأساسي.
  لن يتمكن المستدعون من مطابقته،
  ولكن يمكنك التبديل إلى `%w` في المستقبل إذا لزم الأمر.

عند إضافة سياق للأخطاء المرتجعة، اجعل السياق موجزًا من خلال تجنب
عبارات مثل "فشل في"، والتي تذكر الواضح وتتراكم مع تصاعد الخطأ
من خلال المكدس:

<table>
<thead><tr><th>سيء</th><th>جيد</th></tr></thead>
<tbody>
<tr><td>

```go
s, err := store.New()
if err != nil {
    return fmt.Errorf(
        "failed to create new store: %w", err)
}
```

</td><td>

```go
s, err := store.New()
if err != nil {
    return fmt.Errorf(
        "new store: %w", err)
}
```

</td></tr><tr><td>

```plain
failed to x: failed to y: failed to create new store: the error
```

</td><td>

```plain
x: y: new store: the error
```

</td></tr>
</tbody></table>

ومع ذلك، بمجرد إرسال الخطأ إلى نظام آخر، يجب أن تكون الرسالة واضحة أنها خطأ (مثل علامة `err` أو بادئة "Failed" في السجلات).

انظر أيضًا [لا تتحقق فقط من الأخطاء، تعامل معها بأناقة](https://dave.cheney.net/2016/04/27/dont-just-check-errors-handle-them-gracefully).

#### تسمية الأخطاء

بالنسبة لقيم الخطأ المخزنة كمتغيرات عالمية،
استخدم البادئة `Err` أو `err` اعتمادًا على ما إذا كانت مُصدَّرة.
هذا التوجيه يحل محل [اضافة بادئة _ للمتغيرات العامة غير المصدرة](#اضافة-بادئة-_-للمتغيرات-العامة-غير-المصدرة).

```go
var (
  // الخطأين التاليين مُصدَّران
  // حتى يتمكن مستخدمو هذه الحزمة من مطابقتهما
  // مع errors.Is.

  ErrBrokenLink = errors.New("link is broken")
  ErrCouldNotOpen = errors.New("could not open")

  // هذا الخطأ غير مُصدَّر لأننا
  // لا نريد جعله جزءًا من واجهة API العامة.
  // قد نستخدمه داخل الحزمة
  // مع errors.Is.

  errNotFound = errors.New("not found")
)
```

بالنسبة لأنواع الخطأ المخصصة، استخدم اللاحقة `Error` بدلاً من ذلك.

```go
// وبالمثل، يتم تصدير هذا الخطأ
// حتى يتمكن مستخدمو هذه الحزمة من مطابقته
// مع errors.As.

type NotFoundError struct {
  File string
}

func (e *NotFoundError) Error() string {
  return fmt.Sprintf("file %q not found", e.File)
}

// وهذا الخطأ غير مصدر لأننا
// لا نريد جعله جزءًا من واجهة API العامة.
// يمكننا استخدامه داخل الحزمة
// مع errors.As.

type resolveError struct {
  Path string
}

func (e *resolveError) Error() string {
  return fmt.Sprintf("resolve %q", e.Path)
}
```

#### التعامل مع الأخطاء مرة واحدة

عندما يتلقى مستدعٍ خطأً من مستدعى،
يمكنه التعامل معه بمجموعة متنوعة من الطرق المختلفة
اعتمادًا على ما يعرفه عن الخطأ.

تشمل هذه، على سبيل المثال لا الحصر:

- إذا كان عقد المستدعى يحدد أخطاء محددة،
  مطابقة الخطأ مع `errors.Is` أو `errors.As`
  والتعامل مع الفروع بشكل مختلف
- إذا كان الخطأ قابلاً للاسترداد،
  تسجيل الخطأ والتدهور بأناقة
- إذا كان الخطأ يمثل حالة فشل خاصة بالمجال،
  إرجاع خطأ محدد جيدًا
- إعادة الخطأ، سواء [مغلفًا](#تغليف-الأخطاء) أو حرفيًا

بغض النظر عن كيفية تعامل المستدعي مع الخطأ،
يجب عليه عادةً التعامل مع كل خطأ مرة واحدة فقط.
لا ينبغي للمستدعي، على سبيل المثال، تسجيل الخطأ ثم إعادته،
لأن *مستدعييه* قد يتعاملون مع الخطأ أيضًا.

على سبيل المثال، ضع في اعتبار الحالات التالية:

<table>
<thead><tr><th>الوصف</th><th>الكود</th></tr></thead>
<tbody>
<tr><td>

**سيء**: سجّل الخطأ وأعده

من المحتمل أن يتخذ المستدعون في أعلى المكدس إجراءً مماثلاً مع الخطأ.
القيام بذلك يتسبب في الكثير من الضجيج في سجلات التطبيق مقابل قيمة قليلة.

</td><td>

```go
u, err := getUser(id)
if err != nil {
  // سيء: انظر الوصف
  log.Printf("Could not get user %q: %v", id, err)
  return err
}
```

</td></tr>
<tr><td>

**جيد**: غلّف الخطأ وأعده

سيتعامل المستدعون في أعلى المكدس مع الخطأ.
استخدام `%w` يضمن أنهم يستطيعون مطابقة الخطأ مع `errors.Is` أو `errors.As`
إذا كان ذلك مناسبًا.

</td><td>

```go
u, err := getUser(id)
if err != nil {
  return fmt.Errorf("get user %q: %w", id, err)
}
```

</td></tr>
<tr><td>

**جيد**: سجّل الخطأ وتدهور بأناقة

إذا لم تكن العملية ضرورية بشكل صارم،
يمكننا توفير تجربة متدهورة ولكن غير منقطعة
من خلال التعافي منها.

</td><td>

```go
if err := emitMetrics(); err != nil {
  // فشل كتابة المقاييس لا ينبغي
  // أن يعطل التطبيق.
  log.Printf("Could not emit metrics: %v", err)
}

```

</td></tr>
<tr><td>

**جيد**: طابق الخطأ وتدهور بأناقة

إذا كان المستدعى يحدد خطأ معين في عقده،
وكان الفشل قابلاً للاسترداد،
طابق على حالة الخطأ هذه وتدهور بأناقة.
بالنسبة لجميع الحالات الأخرى، غلّف الخطأ وأعده.

سيتعامل المستدعون في أعلى المكدس مع الأخطاء الأخرى.

</td><td>

```go
tz, err := getUserTimeZone(id)
if err != nil {
  if errors.Is(err, ErrUserNotFound) {
    // المستخدم غير موجود. استخدم UTC.
    tz = time.UTC
  } else {
    return fmt.Errorf("get user %q: %w", id, err)
  }
}
```

</td></tr>
</tbody></table>

### التعامل مع فشل تأكيد النوع

نموذج العودة بقيمة واحدة من [تأكيد النوع](https://go.dev/ref/spec#Type_assertions) سوف يتسبب في حدوث panic عند نوع غير صحيح. لذلك، استخدم دائمًا عبارة "comma ok".

<table>
<thead><tr><th>سيء</th><th>جيد</th></tr></thead>
<tbody>
<tr><td>

```go
t := i.(string)
```

</td><td>

```go
t, ok := i.(string)
if !ok {
  // تعامل مع الخطأ بأناقة
}
```

</td></tr>
</tbody></table>

## لا تستخدم Panic

يجب على الكود الذي يعمل في بيئة الإنتاج تجنب استخدام panic. تعتبر panic مصدرًا رئيسيًا لـ [الإخفاقات المتتالية](https://en.wikipedia.org/wiki/Cascading_failure). إذا حدث خطأ، يجب على الدالة أن تعيد الخطأ وتسمح لمن استدعاها أن يقرر كيفية التعامل معه.

<table>
<thead><tr><th>سيء</th><th>جيد</th></tr></thead>
<tbody>
<tr><td>

```go
func run(args []string) {
  if len(args) == 0 {
    panic("an argument is required")
  }
  // ...
}

func main() {
  run(os.Args[1:])
}
```

</td><td>

```go
func run(args []string) error {
  if len(args) == 0 {
    return errors.New("an argument is required")
  }
  // ...
  return nil
}

func main() {
  if err := run(os.Args[1:]); err != nil {
    fmt.Fprintln(os.Stderr, err)
    os.Exit(1)
  }
}
```

</td></tr>
</tbody></table>

Panic/recover ليست استراتيجية للتعامل مع الأخطاء. يجب أن يحدث panic في البرنامج فقط عندما يحدث شيء لا يمكن إصلاحه مثل إلغاء مرجع nil. الاستثناء لهذه القاعدة هو تهيئة البرنامج: الأشياء السيئة عند بدء تشغيل البرنامج التي يجب أن توقف البرنامج قد تسبب panic.

```go
var _statusTemplate = template.Must(template.New("name").Parse("_statusHTML"))
```

حتى في الاختبارات، يفضل استخدام `t.Fatal` أو `t.FailNow` بدلاً من panic لضمان تحديد الاختبار كفاشل.

<table>
<thead><tr><th>سيء</th><th>جيد</th></tr></thead>
<tbody>
<tr><td>

```go
// func TestFoo(t *testing.T)

f, err := os.CreateTemp("", "test")
if err != nil {
  panic("failed to set up test")
}
```

</td><td>

```go
// func TestFoo(t *testing.T)

f, err := os.CreateTemp("", "test")
if err != nil {
  t.Fatal("failed to set up test")
}
```

</td></tr>
</tbody></table>

## استخدم go.uber.org/atomic

العمليات الذرية باستخدام حزمة [sync/atomic](https://pkg.go.dev/sync/atomic) تعمل على الأنواع الأساسية (`int32`، `int64`، إلخ) مما يجعل من السهل نسيان استخدام العملية الذرية لقراءة أو تعديل المتغيرات.

[go.uber.org/atomic](https://pkg.go.dev/go.uber.org/atomic) يضيف أمان الأنواع لهذه العمليات عن طريق إخفاء النوع الأساسي. بالإضافة إلى ذلك، يتضمن نوع `atomic.Bool` المريح.

<table>
<thead><tr><th>سيء</th><th>جيد</th></tr></thead>
<tbody>
<tr><td>

```go
type foo struct {
  running int32  // atomic
}

func (f* foo) start() {
  if atomic.SwapInt32(&f.running, 1) == 1 {
     // already running…
     return
  }
  // start the Foo
}

func (f *foo) isRunning() bool {
  return f.running == 1  // race!
}
```

</td><td>

```go
type foo struct {
  running atomic.Bool
}

func (f *foo) start() {
  if f.running.Swap(true) {
     // already running…
     return
  }
  // start the Foo
}

func (f *foo) isRunning() bool {
  return f.running.Load()
}
```

</td></tr>
</tbody></table>

## تجنب المتغيرات العامة القابلة للتغيير

تجنب تغيير المتغيرات العامة، واستخدم حقن التبعيات بدلاً من ذلك. هذا ينطبق على مؤشرات الدوال وكذلك على أنواع القيم الأخرى.

<table>
<thead><tr><th>سيء</th><th>جيد</th></tr></thead>
<tbody>
<tr><td>

```go
// sign.go

var _timeNow = time.Now

func sign(msg string) string {
  now := _timeNow()
  return signWithTime(msg, now)
}
```

</td><td>

```go
// sign.go

type signer struct {
  now func() time.Time
}

func newSigner() *signer {
  return &signer{
    now: time.Now,
  }
}

func (s *signer) Sign(msg string) string {
  now := s.now()
  return signWithTime(msg, now)
}
```

</td></tr>
<tr><td>

```go
// sign_test.go

func TestSign(t *testing.T) {
  oldTimeNow := _timeNow
  _timeNow = func() time.Time {
    return someFixedTime
  }
  defer func() { _timeNow = oldTimeNow }()

  assert.Equal(t, want, sign(give))
}
```

</td><td>

```go
// sign_test.go

func TestSigner(t *testing.T) {
  s := newSigner()
  s.now = func() time.Time {
    return someFixedTime
  }

  assert.Equal(t, want, s.Sign(give))
}
```

</td></tr>
</tbody></table>

## تجنب تضمين الأنواع في الهياكل العامة

هذه الأنواع المضمنة تسرب تفاصيل التنفيذ، وتعيق تطور النوع، وتخفي التوثيق.

بافتراض أنك قمت بتنفيذ مجموعة متنوعة من أنواع القوائم باستخدام `AbstractList` مشترك، تجنب تضمين `AbstractList` في تنفيذاتك الملموسة للقائمة.
بدلاً من ذلك، اكتب يدويًا فقط الطرق لقائمتك الملموسة التي ستفوض إلى القائمة المجردة.

```go
type AbstractList struct {}

// Add adds an entity to the list.
func (l *AbstractList) Add(e Entity) {
  // ...
}

// Remove removes an entity from the list.
func (l *AbstractList) Remove(e Entity) {
  // ...
}
```

<table>
<thead><tr><th>سيء</th><th>جيد</th></tr></thead>
<tbody>
<tr><td>

```go
// ConcreteList is a list of entities.
type ConcreteList struct {
  *AbstractList
}
```

</td><td>

```go
// ConcreteList is a list of entities.
type ConcreteList struct {
  list *AbstractList
}

// Add adds an entity to the list.
func (l *ConcreteList) Add(e Entity) {
  l.list.Add(e)
}

// Remove removes an entity from the list.
func (l *ConcreteList) Remove(e Entity) {
  l.list.Remove(e)
}
```

</td></tr>
</tbody></table>

تسمح Go بـ [تضمين النوع](https://go.dev/doc/effective_go#embedding) كحل وسط بين الوراثة والتركيب. النوع الخارجي يحصل على نسخ ضمنية من طرق النوع المضمن. هذه الطرق، بشكل افتراضي، تفوض إلى نفس الطريقة للمثيل المضمن.

يكتسب الهيكل أيضًا حقلاً بنفس اسم النوع. لذا، إذا كان النوع المضمن عامًا، فإن الحقل يكون عامًا. للحفاظ على التوافق الخلفي، يجب أن تحتفظ كل نسخة مستقبلية من النوع الخارجي بالنوع المضمن.

نادرًا ما يكون النوع المضمن ضروريًا. إنه وسيلة مريحة تساعدك على تجنب كتابة طرق التفويض المملة.

حتى تضمين واجهة AbstractList *متوافقة*، بدلاً من الهيكل، سيوفر للمطور مرونة أكبر للتغيير في المستقبل، ولكنه سيظل يُسرب تفاصيل أن القوائم الملموسة تستخدم تنفيذًا مجردًا.

<table>
<thead><tr><th>سيء</th><th>جيد</th></tr></thead>
<tbody>
<tr><td>

```go
// AbstractList is a generalized implementation
// for various kinds of lists of entities.
type AbstractList interface {
  Add(Entity)
  Remove(Entity)
}

// ConcreteList is a list of entities.
type ConcreteList struct {
  AbstractList
}
```

</td><td>

```go
// ConcreteList is a list of entities.
type ConcreteList struct {
  list AbstractList
}

// Add adds an entity to the list.
func (l *ConcreteList) Add(e Entity) {
  l.list.Add(e)
}

// Remove removes an entity from the list.
func (l *ConcreteList) Remove(e Entity) {
  l.list.Remove(e)
}
```

</td></tr>
</tbody></table>

سواء مع هيكل مضمن أو واجهة مضمنة، يضع النوع المضمن قيودًا على تطور النوع.

- إضافة طرق إلى واجهة مضمنة هو تغيير كاسر.
- إزالة طرق من هيكل مضمن هو تغيير كاسر.
- إزالة النوع المضمن هو تغيير كاسر.
- استبدال النوع المضمن، حتى ببديل يلبي نفس الواجهة، هو تغيير كاسر.

على الرغم من أن كتابة طرق التفويض هذه أمر مُمل، إلا أن الجهد الإضافي يخفي تفاصيل التنفيذ، ويترك المزيد من الفرص للتغيير، ويزيل أيضًا الاتجاه غير المباشر لاكتشاف واجهة List الكاملة في التوثيق.

## تجنب استخدام الأسماء المدمجة

[مواصفات لغة Go](https://go.dev/ref/spec) تحدد العديد من [المعرفات المعلنة مسبقًا](https://go.dev/ref/spec#Predeclared_identifiers) المدمجة التي لا ينبغي استخدامها كأسماء داخل برامج Go.

اعتمادًا على السياق، فإن إعادة استخدام هذه المعرفات كأسماء إما ستظل الأصل داخل النطاق المعجمي الحالي (وأي نطاقات متداخلة) أو تجعل الكود المتأثر مربكًا. في أفضل الحالات، سيشتكي المُجمِّع؛ وفي أسوأ الحالات، قد يقدم مثل هذا الكود أخطاء كامنة يصعب اكتشافها.

<table>
<thead><tr><th>سيء</th><th>جيد</th></tr></thead>
<tbody>
<tr><td>

```go
var error string
// `error` يظلل المدمج

// أو

func handleErrorMessage(error string) {
    // `error` يظلل المدمج
}
```

</td><td>

```go
var errorMessage string
// `error` يشير إلى المدمج

// أو

func handleErrorMessage(msg string) {
    // `error` يشير إلى المدمج
}
```

</td></tr>
<tr><td>

```go
type Foo struct {
    // بينما هذه الحقول تقنيًا لا تشكل
    // تظليلًا، إلا أن البحث عن
    // `error` أو `string` يصبح
    // غامضًا الآن.
    error  error
    string string
}

func (f Foo) Error() error {
    // `error` و `f.error` متشابهان 
    // بصريًا
    return f.error
}

func (f Foo) String() string {
    // `string` و `f.string` متشابهان
    // بصريًا
    return f.string
}
```

</td><td>

```go
type Foo struct {
    // `error` و `string` أصبحا
    // غير غامضين الآن.
    err error
    str string
}

func (f Foo) Error() error {
    return f.err
}

func (f Foo) String() string {
    return f.str
}
```

</td></tr>
</tbody></table>

لاحظ أن المُجمِّع لن ينتج أخطاء عند استخدام المعرفات المعلنة مسبقًا، ولكن الأدوات مثل `go vet` يجب أن تشير بشكل صحيح إلى هذه الحالات وغيرها من حالات التظليل.

## تجنب `init()`

تجنب `init()` حيثما أمكن. عندما يكون `init()` لا مفر منه أو مرغوبًا فيه، يجب أن يحاول الكود:

1. أن يكون حتميًا تمامًا، بغض النظر عن بيئة البرنامج أو الاستدعاء.
2. تجنب الاعتماد على ترتيب أو آثار جانبية لوظائف `init()` أخرى. بينما ترتيب `init()` معروف جيدًا، يمكن أن يتغير الكود، وبالتالي فإن العلاقات بين وظائف `init()` يمكن أن تجعل الكود هشًا وعرضة للأخطاء.
3. تجنب الوصول إلى أو التلاعب بالحالة العالمية أو البيئية، مثل معلومات الجهاز، متغيرات البيئة، دليل العمل، وسيطات/مدخلات البرنامج، إلخ.
4. تجنب I/O، بما في ذلك نظام الملفات والشبكة واستدعاءات النظام.

الكود الذي لا يستطيع تلبية هذه المتطلبات من المحتمل أن ينتمي كمساعد ليتم استدعاؤه كجزء من `main()` (أو في مكان آخر في دورة حياة البرنامج)، أو يتم كتابته كجزء من `main()` نفسه. على وجه الخصوص، يجب أن تولي المكتبات التي يُقصد استخدامها من قبل برامج أخرى عناية خاصة لتكون حتمية تمامًا وعدم تنفيذ "سحر init".

<table>
<thead><tr><th>سيء</th><th>جيد</th></tr></thead>
<tbody>
<tr><td>

```go
type Foo struct {
    // ...
}

var _defaultFoo Foo

func init() {
    _defaultFoo = Foo{
        // ...
    }
}
```

</td><td>

```go
var _defaultFoo = Foo{
    // ...
}

// أو، بشكل أفضل، لإمكانية الاختبار:

var _defaultFoo = defaultFoo()

func defaultFoo() Foo {
    return Foo{
        // ...
    }
}
```

</td></tr>
<tr><td>

```go
type Config struct {
    // ...
}

var _config Config

func init() {
    // سيء: يعتمد على الدليل الحالي
    cwd, _ := os.Getwd()

    // سيء: I/O
    raw, _ := os.ReadFile(
        path.Join(cwd, "config", "config.yaml"),
    )

    yaml.Unmarshal(raw, &_config)
}
```

</td><td>

```go
type Config struct {
    // ...
}

func loadConfig() Config {
    cwd, err := os.Getwd()
    // معالجة الخطأ

    raw, err := os.ReadFile(
        path.Join(cwd, "config", "config.yaml"),
    )
    // معالجة الخطأ

    var config Config
    yaml.Unmarshal(raw, &config)

    return config
}
```

</td></tr>
</tbody></table>

مع مراعاة ما سبق، بعض الحالات التي قد يكون فيها `init()` مفضلاً أو ضروريًا قد تشمل:

- التعبيرات المعقدة التي لا يمكن تمثيلها كتعيينات مفردة.
- الخطافات القابلة للتوصيل، مثل لهجات `database/sql`، سجلات نوع الترميز، وما إلى ذلك.
- التحسينات لـ [Google Cloud Functions](https://cloud.google.com/functions/docs/bestpractices/tips#use_global_variables_to_reuse_objects_in_future_invocations) وأشكال أخرى من الحساب المسبق الحتمي.

## الخروج في الدالة Main

تستخدم برامج Go [`os.Exit`](https://pkg.go.dev/os#Exit) أو [`log.Fatal*`](https://pkg.go.dev/log#Fatal) للخروج فورًا. (Panicking ليست طريقة جيدة للخروج من البرامج، [لا تستخدم panic](#لا-تستخدم-panic).)

استدع `os.Exit` أو `log.Fatal*` **فقط في `main()`**. يجب أن تعيد جميع الدوال الأخرى أخطاء للإشارة إلى الفشل.

<table>
<thead><tr><th>سيء</th><th>جيد</th></tr></thead>
<tbody>
<tr><td>

```go
func main() {
  body := readFile(path)
  fmt.Println(body)
}

func readFile(path string) string {
  f, err := os.Open(path)
  if err != nil {
    log.Fatal(err)
  }

  b, err := io.ReadAll(f)
  if err != nil {
    log.Fatal(err)
  }

  return string(b)
}
```

</td><td>

```go
func main() {
  body, err := readFile(path)
  if err != nil {
    log.Fatal(err)
  }
  fmt.Println(body)
}

func readFile(path string) (string, error) {
  f, err := os.Open(path)
  if err != nil {
    return "", err
  }

  b, err := io.ReadAll(f)
  if err != nil {
    return "", err
  }

  return string(b), nil
}
```

</td></tr>
</tbody></table>

المنطق: البرامج التي تحتوي على وظائف متعددة تخرج تقدم بعض المشكلات:

- تدفق التحكم غير الواضح: أي وظيفة يمكنها إنهاء البرنامج لذا يصبح من الصعب التفكير في تدفق التحكم.
- صعب الاختبار: الوظيفة التي تنهي البرنامج ستنهي أيضًا الاختبار الذي يستدعيها. هذا يجعل الوظيفة صعبة الاختبار ويقدم مخاطر تخطي اختبارات أخرى لم يتم تشغيلها بعد بواسطة `go test`.
- تخطي التنظيف: عندما تخرج وظيفة من البرنامج، فإنها تتخطى استدعاءات الوظيفة المدرجة في قائمة الانتظار مع بيانات `defer`. هذا يضيف مخاطر تخطي مهام التنظيف المهمة.

### الخروج مرة واحدة

إذا أمكن، فضل استدعاء `os.Exit` أو `log.Fatal` **مرة واحدة على الأكثر** في `main()`. إذا كانت هناك سيناريوهات خطأ متعددة توقف تنفيذ البرنامج، ضع هذا المنطق تحت وظيفة منفصلة وأعد الأخطاء منها.

هذا له تأثير تقصير وظيفة `main()` ووضع جميع منطق الأعمال الرئيسي في وظيفة منفصلة قابلة للاختبار.

<table>
<thead><tr><th>سيء</th><th>جيد</th></tr></thead>
<tbody>
<tr><td>

```go
package main

func main() {
  args := os.Args[1:]
  if len(args) != 1 {
    log.Fatal("missing file")
  }
  name := args[0]

  f, err := os.Open(name)
  if err != nil {
    log.Fatal(err)
  }
  defer f.Close()

  // إذا استدعينا log.Fatal بعد هذا السطر،
  // لن يتم استدعاء f.Close.

  b, err := io.ReadAll(f)
  if err != nil {
    log.Fatal(err)
  }

  // ...
}
```

</td><td>

```go
package main

func main() {
  if err := run(); err != nil {
    log.Fatal(err)
  }
}

func run() error {
  args := os.Args[1:]
  if len(args) != 1 {
    return errors.New("missing file")
  }
  name := args[0]

  f, err := os.Open(name)
  if err != nil {
    return err
  }
  defer f.Close()

  b, err := io.ReadAll(f)
  if err != nil {
    return err
  }

  // ...
}
```

</td></tr>
</tbody></table>

المثال أعلاه يستخدم `log.Fatal`، لكن التوجيه ينطبق أيضًا على `os.Exit` أو أي كود مكتبة يستدعي `os.Exit`.

```go
func main() {
  if err := run(); err != nil {
    fmt.Fprintln(os.Stderr, err)
    os.Exit(1)
  }
}
```

يمكنك تغيير توقيع `run()` ليناسب احتياجاتك. على سبيل المثال، إذا كان برنامجك يجب أن يخرج برموز خروج محددة للأخطاء، فقد يعيد `run()` رمز الخروج بدلاً من الخطأ. هذا يسمح أيضًا لاختبارات الوحدة بالتحقق من هذا السلوك مباشرة.

```go
func main() {
  os.Exit(run(args))
}

func run() (exitCode int) {
  // ...
}
```

بشكل أعم، لاحظ أن الوظيفة `run()` المستخدمة في هذه الأمثلة ليست مقصودة لتكون وصفية. هناك مرونة في الاسم والتوقيع وإعداد وظيفة `run()`. من بين أمور أخرى، يمكنك:

- قبول وسيطات سطر الأوامر غير المحللة (مثل `run(os.Args[1:])`)
- تحليل وسيطات سطر الأوامر في `main()` وتمريرها إلى `run`
- استخدام نوع خطأ مخصص لنقل رمز الخروج مرة أخرى إلى `main()`
- وضع منطق الأعمال في طبقة مختلفة من التجريد من `package main`

يتطلب هذا التوجيه فقط أن يكون هناك مكان واحد في `main()` مسؤول عن الخروج الفعلي من العملية.

## استخدام علامات الحقل في الهياكل المحولة

أي حقل هيكل يتم تحويله إلى JSON أو YAML أو تنسيقات أخرى تدعم تسمية الحقول المستندة إلى العلامات يجب أن يكون مزودًا بالعلامة ذات الصلة.

<table>
<thead><tr><th>سيء</th><th>جيد</th></tr></thead>
<tbody>
<tr><td>

```go
type Stock struct {
  Price int
  Name  string
}

bytes, err := json.Marshal(Stock{
  Price: 137,
  Name:  "UBER",
})
```

</td><td>

```go
type Stock struct {
  Price int    `json:"price"`
  Name  string `json:"name"`
  // آمن لإعادة تسمية Name إلى Symbol.
}

bytes, err := json.Marshal(Stock{
  Price: 137,
  Name:  "UBER",
})
```

</td></tr>
</tbody></table>

المنطق: الشكل المتسلسل للهيكل هو عقد بين أنظمة مختلفة. التغييرات في بنية الشكل المتسلسل - بما في ذلك أسماء الحقول - تكسر هذا العقد. تحديد أسماء الحقول داخل العلامات يجعل العقد صريحًا، ويحمي من كسر العقد عن طريق الخطأ من خلال إعادة التصميم أو إعادة تسمية الحقول.


### لا تطلق goroutines دون متابعة

الـ Goroutines خفيفة الوزن، لكنها ليست مجانية:
على الأقل، تستهلك ذاكرة للـ stack الخاص بها و CPU للجدولة.
بينما هذه التكاليف صغيرة للاستخدامات النموذجية للـ goroutines،
يمكن أن تسبب مشاكل أداء كبيرة
عندما يتم إطلاقها بأعداد كبيرة دون التحكم في دورة حياتها.
يمكن للـ goroutines ذات دورة الحياة غير المُدارة أن تسبب أيضًا مشاكل أخرى
مثل منع الكائنات غير المستخدمة من جمع القمامة
والاحتفاظ بالموارد التي لم تعد مستخدمة.

لذلك، لا تسرب goroutines في كود الإنتاج.
استخدم [go.uber.org/goleak](https://pkg.go.dev/go.uber.org/goleak)
لاختبار تسريبات goroutine داخل الحزم التي قد تطلق goroutines.

بشكل عام، كل goroutine:

- يجب أن يكون لها وقت متوقع ستتوقف فيه عن التشغيل؛ أو
- يجب أن يكون هناك طريقة للإشارة إلى الـ goroutine بأنه يجب أن تتوقف

في كلتا الحالتين، يجب أن تكون هناك طريقة للكود لحظر وانتظار انتهاء الـ goroutine.

على سبيل المثال:

<table>
<thead><tr><th>سيء</th><th>جيد</th></tr></thead>
<tbody>
<tr><td>

```go
go func() {
  for {
    flush()
    time.Sleep(delay)
  }
}()
```

</td><td>

```go
var (
  stop = make(chan struct{}) // يخبر الـ goroutine بالتوقف
  done = make(chan struct{}) // يخبرنا أن الـ goroutine خرجت
)
go func() {
  defer close(done)

  ticker := time.NewTicker(delay)
  defer ticker.Stop()
  for {
    select {
    case <-ticker.C:
      flush()
    case <-stop:
      return
    }
  }
}()

// في مكان آخر...
close(stop)  // إشارة للـ goroutine بالتوقف
<-done       // وانتظارها حتى تخرج
```

</td></tr>
<tr><td>

لا توجد طريقة لإيقاف هذه الـ goroutine.
ستعمل حتى يخرج التطبيق.

</td><td>

يمكن إيقاف هذه الـ goroutine بـ `close(stop)`،
ويمكننا انتظار خروجها بـ `<-done`.

</td></tr>
</tbody></table>

#### انتظر خروج goroutines

بالنظر إلى goroutine تم إطلاقها بواسطة النظام،
يجب أن تكون هناك طريقة لانتظار خروج الـ goroutine.
هناك طريقتان شائعتان للقيام بذلك:

- استخدام `sync.WaitGroup`.
  افعل هذا إذا كانت هناك عدة goroutines تريد الانتظار لها

  ```go
  var wg sync.WaitGroup
  for i := 0; i < N; i++ {
    wg.Add(1)
    go func() {
      defer wg.Done()
      // ...
    }()
  }

  // للانتظار حتى تنتهي جميعها:
  wg.Wait()
  ```

- أضف `chan struct{}` آخر تغلقه الـ goroutine عندما تنتهي.
  افعل هذا إذا كانت هناك goroutine واحدة فقط.

  ```go
  done := make(chan struct{})
  go func() {
    defer close(done)
    // ...
  }()

  // للانتظار حتى تنتهي الـ goroutine:
  <-done
  ```

#### لا تضع goroutines في `init()`

يجب ألا تطلق وظائف `init()` goroutines.
انظر أيضًا [تجنب init()](#تجنب-init).

إذا احتاجت حزمة إلى goroutine خلفية،
يجب أن تعرض كائنًا مسؤولًا عن إدارة دورة حياة goroutine.
يجب أن يوفر الكائن طريقة (`Close`، `Stop`، `Shutdown`، إلخ)
تشير إلى الـ goroutine الخلفية بالتوقف، وتنتظر خروجها.

<table>
<thead><tr><th>سيء</th><th>جيد</th></tr></thead>
<tbody>
<tr><td>

```go
func init() {
  go doWork()
}

func doWork() {
  for {
    // ...
  }
}
```

</td><td>

```go
type Worker struct{ /* ... */ }

func NewWorker(...) *Worker {
  w := &Worker{
    stop: make(chan struct{}),
    done: make(chan struct{}),
    // ...
  }
  go w.doWork()
}

func (w *Worker) doWork() {
  defer close(w.done)
  for {
    // ...
    case <-w.stop:
      return
  }
}

// Shutdown يخبر العامل بالتوقف
// وينتظر حتى ينتهي.
func (w *Worker) Shutdown() {
  close(w.stop)
  <-w.done
}
```

</td></tr>
<tr><td>

تطلق goroutine خلفية بشكل غير مشروط عندما يستورد المستخدم هذه الحزمة.
ليس لدى المستخدم أي تحكم في الـ goroutine أو وسيلة لإيقافها.

</td><td>

تطلق العامل فقط إذا طلب المستخدم ذلك.
توفر وسيلة لإيقاف العامل بحيث يمكن للمستخدم تحرير الموارد المستخدمة بواسطة العامل.

لاحظ أنه يجب عليك استخدام `WaitGroup`s إذا كان العامل يدير عدة goroutines.
انظر [انتظر خروج goroutines](#انتظر-خروج-goroutines).

</td></tr>
</tbody></table>

## الأداء

تنطبق إرشادات الأداء المحددة فقط على المسار الساخن (hot path).

### تفضيل strconv على fmt

عند تحويل الأنواع البدائية من/إلى سلاسل نصية، `strconv` أسرع من
`fmt`.

<table>
<thead><tr><th>سيء</th><th>جيد</th></tr></thead>
<tbody>
<tr><td>

```go
for i := 0; i < b.N; i++ {
  s := fmt.Sprint(rand.Int())
}
```

</td><td>

```go
for i := 0; i < b.N; i++ {
  s := strconv.Itoa(rand.Int())
}
```

</td></tr>
<tr><td>

```plain
BenchmarkFmtSprint-4    143 ns/op    2 allocs/op
```

</td><td>

```plain
BenchmarkStrconv-4    64.2 ns/op    1 allocs/op
```

</td></tr>
</tbody></table>

### تجنب التحويلات المتكررة من النص إلى البايت

لا تقم بإنشاء شرائح بايت من سلسلة ثابتة بشكل متكرر. بدلاً من ذلك، قم بالتحويل مرة واحدة والتقط النتيجة.

<table>
<thead><tr><th>سيء</th><th>جيد</th></tr></thead>
<tbody>
<tr><td>

```go
for i := 0; i < b.N; i++ {
  w.Write([]byte("Hello world"))
}
```

</td><td>

```go
data := []byte("Hello world")
for i := 0; i < b.N; i++ {
  w.Write(data)
}
```

</td></tr>
<tr><td>

```plain
BenchmarkBad-4   50000000   22.2 ns/op
```

</td><td>

```plain
BenchmarkGood-4  500000000   3.25 ns/op
```

</td></tr>
</tbody></table>

### تفضيل تحديد سعة الحاويات

حدد سعة الحاوية حيثما أمكن من أجل تخصيص ذاكرة للحاوية مقدمًا. هذا يقلل من التخصيصات اللاحقة (عن طريق نسخ وتغيير حجم الحاوية) مع إضافة العناصر.

#### تحديد تلميحات سعة Map

حيثما أمكن، قدم تلميحات السعة عند تهيئة Maps باستخدام `make()`.

```go
make(map[T1]T2, hint)
```

توفير تلميح سعة لـ `make()` يحاول تحجيم Map بشكل صحيح في وقت التهيئة، مما يقلل الحاجة إلى تنمية Map والتخصيصات مع إضافة عناصر إلى Map.

لاحظ أنه، على عكس الشرائح، لا تضمن تلميحات سعة Map التخصيص الكامل والاستباقي، ولكنها تستخدم لتقريب عدد دلاء خريطة التجزئة المطلوبة. ونتيجة لذلك، قد تظل التخصيصات تحدث عند إضافة عناصر إلى Map، حتى تصل إلى السعة المحددة.

<table>
<thead><tr><th>سيء</th><th>جيد</th></tr></thead>
<tbody>
<tr><td>

```go
m := make(map[string]os.FileInfo)

files, _ := os.ReadDir("./files")
for _, f := range files {
    m[f.Name()] = f
}
```

</td><td>

```go

files, _ := os.ReadDir("./files")

m := make(map[string]os.DirEntry, len(files))
for _, f := range files {
    m[f.Name()] = f
}
```

</td></tr>
<tr><td>

يتم إنشاء `m` بدون تلميح حجم؛ قد تكون هناك تخصيصات أكثر في وقت التعيين.

</td><td>

يتم إنشاء `m` مع تلميح حجم؛ قد تكون هناك تخصيصات أقل في وقت التعيين.

</td></tr>
</tbody></table>

#### تحديد سعة Slice

حيثما أمكن، قدم تلميحات السعة عند تهيئة الشرائح باستخدام `make()`، خاصة عند الإلحاق.

```go
make([]T, length, capacity)
```

على عكس Maps، سعة الشريحة ليست تلميحًا: سيخصص المُجمِّع ذاكرة كافية لسعة الشريحة كما هو مقدم إلى `make()`، مما يعني أن عمليات `append()` اللاحقة لن تتكبد أي تخصيصات (حتى يتطابق طول الشريحة مع السعة، وبعد ذلك ستتطلب أي عمليات إلحاق تغيير حجم لاحتواء عناصر إضافية).

<table>
<thead><tr><th>سيء</th><th>جيد</th></tr></thead>
<tbody>
<tr><td>

```go
for n := 0; n < b.N; n++ {
  data := make([]int, 0)
  for k := 0; k < size; k++{
    data = append(data, k)
  }
}
```

</td><td>

```go
for n := 0; n < b.N; n++ {
  data := make([]int, 0, size)
  for k := 0; k < size; k++{
    data = append(data, k)
  }
}
```

</td></tr>
<tr><td>

```plain
BenchmarkBad-4    100000000    2.48s
```

</td><td>

```plain
BenchmarkGood-4   100000000    0.21s
```

</td></tr>
</tbody></table>

## الأسلوب

### تجنب الأسطر الطويلة جدًا

تجنب أسطر الكود التي تتطلب من القراء التمرير أفقيًا أو إدارة رؤوسهم كثيرًا.

نوصي بحد طول سطر ليّن يبلغ **99 حرفًا**. يجب على المؤلفين أن يهدفوا إلى لف الأسطر قبل الوصول إلى هذا الحد، ولكنه ليس حدًا صارمًا. يُسمح للكود بتجاوز هذا الحد.

### كن متسقًا

يمكن تقييم بعض الإرشادات المحددة في هذا المستند بشكل موضوعي؛ البعض الآخر ظرفي أو سياقي أو ذاتي.

فوق كل شيء آخر، **كن متسقًا**.

الكود المتسق أسهل في الصيانة، وأسهل في التبرير، ويتطلب تكلفة معرفية أقل، وأسهل في الترحيل أو التحديث مع ظهور اتفاقيات جديدة أو إصلاح فئات من الأخطاء.

وعلى العكس من ذلك، فإن وجود أساليب متباينة أو متعارضة متعددة ضمن قاعدة شفرة واحدة يتسبب في تكاليف صيانة، وعدم يقين، وتنافر معرفي، وكل ذلك يمكن أن يساهم بشكل مباشر في سرعة أقل، ومراجعات كود مؤلمة، وأخطاء.

عند تطبيق هذه الإرشادات على قاعدة شفرة، يُوصى بإجراء التغييرات على مستوى الحزمة (أو أكبر): التطبيق على مستوى أقل من الحزمة ينتهك المخاوف المذكورة أعلاه من خلال إدخال أنماط متعددة في نفس الكود.

### تجميع التصريحات المتشابهة

تدعم Go تجميع التصريحات المتشابهة.

<table>
<thead><tr><th>سيء</th><th>جيد</th></tr></thead>
<tbody>
<tr><td>

```go
import "a"
import "b"
```

</td><td>

```go
import (
  "a"
  "b"
)
```

</td></tr>
</tbody></table>

هذا ينطبق أيضًا على الثوابت والمتغيرات وتعريفات الأنواع.

<table>
<thead><tr><th>سيء</th><th>جيد</th></tr></thead>
<tbody>
<tr><td>

```go

const a = 1
const b = 2



var a = 1
var b = 2



type Area float64
type Volume float64
```

</td><td>

```go
const (
  a = 1
  b = 2
)

var (
  a = 1
  b = 2
)

type (
  Area float64
  Volume float64
)
```

</td></tr>
</tbody></table>

جمّع فقط التصريحات المتعلقة ببعضها. لا تجمّع التصريحات التي لا علاقة لها ببعضها.

<table>
<thead><tr><th>سيء</th><th>جيد</th></tr></thead>
<tbody>
<tr><td>

```go
type Operation int

const (
  Add Operation = iota + 1
  Subtract
  Multiply
  EnvVar = "MY_ENV"
)
```

</td><td>

```go
type Operation int

const (
  Add Operation = iota + 1
  Subtract
  Multiply
)

const EnvVar = "MY_ENV"
```

</td></tr>
</tbody></table>

المجموعات غير محدودة في الأماكن التي يمكن استخدامها فيها. على سبيل المثال، يمكنك استخدامها داخل الدوال.

<table>
<thead><tr><th>سيء</th><th>جيد</th></tr></thead>
<tbody>
<tr><td>

```go
func f() string {
  red := color.New(0xff0000)
  green := color.New(0x00ff00)
  blue := color.New(0x0000ff)

  // ...
}
```

</td><td>

```go
func f() string {
  var (
    red   = color.New(0xff0000)
    green = color.New(0x00ff00)
    blue  = color.New(0x0000ff)
  )

  // ...
}
```

</td></tr>
</tbody></table>

استثناء: يجب تجميع تعريفات المتغيرات، خاصة داخل الدوال، معًا إذا تم تعريفها بجوار متغيرات أخرى. افعل هذا للمتغيرات المعلنة معًا حتى لو كانت غير مرتبطة.

<table>
<thead><tr><th>سيء</th><th>جيد</th></tr></thead>
<tbody>
<tr><td>

```go
func (c *client) request() {
  caller := c.name
  format := "json"
  timeout := 5*time.Second
  var err error

  // ...
}
```

</td><td>

```go
func (c *client) request() {
  var (
    caller  = c.name
    format  = "json"
    timeout = 5*time.Second
    err error
  )

  // ...
}
```

</td></tr>
</tbody></table>

### ترتيب مجموعات الاستيراد

يجب أن تكون هناك مجموعتان للاستيراد:

- المكتبة القياسية
- كل شيء آخر

هذا هو التجميع المطبق بواسطة goimports بشكل افتراضي.

<table>
<thead><tr><th>سيء</th><th>جيد</th></tr></thead>
<tbody>
<tr><td>

```go
import (
  "fmt"
  "os"
  "go.uber.org/atomic"
  "golang.org/x/sync/errgroup"
)
```

</td><td>

```go
import (
  "fmt"
  "os"

  "go.uber.org/atomic"
  "golang.org/x/sync/errgroup"
)
```

</td></tr>
</tbody></table>

### أسماء الحزم

عند تسمية الحزم، اختر اسمًا يكون:

- كل الحروف صغيرة. لا حروف كبيرة أو شرطات سفلية.
- لا يحتاج إلى إعادة تسمية باستخدام الاستيرادات المسماة في معظم مواقع الاستدعاء.
- قصير وموجز. تذكر أن الاسم يتم تحديده بالكامل في كل موقع استدعاء.
- ليس بصيغة الجمع. على سبيل المثال، `net/url`، وليس `net/urls`.
- ليس "common" أو "util" أو "shared" أو "lib". هذه أسماء سيئة وغير مفيدة.

انظر أيضًا [Package Names](https://go.dev/blog/package-names) و [Style guideline for Go packages](https://rakyll.org/style-packages/).

### أسماء الدوال

نتبع اتفاقية مجتمع Go في استخدام [MixedCaps لأسماء الدوال](https://go.dev/doc/effective_go#mixed-caps). يتم استثناء وظائف الاختبار، التي قد تحتوي على شرطات سفلية لغرض تجميع حالات الاختبار ذات الصلة، مثل
`TestMyFunction_WhatIsBeingTested`.

### استخدام الأسماء المستعارة في الاستيراد

يجب استخدام التسمية المستعارة للاستيراد إذا كان اسم الحزمة لا يتطابق مع العنصر الأخير من مسار الاستيراد.

```go
import (
  "net/http"

  client "example.com/client-go"
  trace "example.com/trace/v2"
)
```

في جميع السيناريوهات الأخرى، يجب تجنب الأسماء المستعارة للاستيراد ما لم يكن هناك تعارض مباشر بين الاستيرادات.

<table>
<thead><tr><th>سيء</th><th>جيد</th></tr></thead>
<tbody>
<tr><td>

```go
import (
  "fmt"
  "os"
  runtimetrace "runtime/trace"

  nettrace "golang.net/x/trace"
)
```

</td><td>

```go
import (
  "fmt"
  "os"
  "runtime/trace"

  nettrace "golang.net/x/trace"
)
```

</td></tr>
</tbody></table>

### تجميع وترتيب الدوال

- يجب ترتيب الدوال بترتيب الاستدعاء التقريبي.
- يجب تجميع الدوال في ملف حسب المستقبل.

لذلك، يجب أن تظهر الدوال المصدّرة أولاً في الملف، بعد تعريفات `struct` و `const` و `var`.

قد يظهر `newXYZ()`/`NewXYZ()` بعد تعريف النوع، ولكن قبل باقي الطرق على المستقبل.

بما أن الدوال مجمعة حسب المستقبل، يجب أن تظهر دوال المساعدة البسيطة نحو نهاية الملف.

<table>
<thead><tr><th>سيء</th><th>جيد</th></tr></thead>
<tbody>
<tr><td>

```go
func (s *something) Cost() {
  return calcCost(s.weights)
}

type something struct{ ... }

func calcCost(n []int) int {...}

func (s *something) Stop() {...}

func newSomething() *something {
    return &something{}
}
```

</td><td>

```go
type something struct{ ... }

func newSomething() *something {
    return &something{}
}

func (s *something) Cost() {
  return calcCost(s.weights)
}

func (s *something) Stop() {...}

func calcCost(n []int) int {...}
```

</td></tr>
</tbody></table>

### تقليل التداخل

يجب أن يقلل الكود من التداخل حيثما أمكن من خلال التعامل مع حالات الخطأ/الشروط الخاصة أولاً والعودة مبكرًا أو مواصلة الحلقة. قلل من كمية الكود المتداخل على مستويات متعددة.

<table>
<thead><tr><th>سيء</th><th>جيد</th></tr></thead>
<tbody>
<tr><td>

```go
for _, v := range data {
  if v.F1 == 1 {
    v = process(v)
    if err := v.Call(); err == nil {
      v.Send()
    } else {
      return err
    }
  } else {
    log.Printf("Invalid v: %v", v)
  }
}
```

</td><td>

```go
for _, v := range data {
  if v.F1 != 1 {
    log.Printf("Invalid v: %v", v)
    continue
  }

  v = process(v)
  if err := v.Call(); err != nil {
    return err
  }
  v.Send()
}
```

</td></tr>
</tbody></table>

### تجنب استخدام Else غير الضروري

إذا تم تعيين متغير في كلا فرعي if، يمكن استبداله بـ if واحد.

<table>
<thead><tr><th>سيء</th><th>جيد</th></tr></thead>
<tbody>
<tr><td>

```go
var a int
if b {
  a = 100
} else {
  a = 10
}
```

</td><td>

```go
a := 10
if b {
  a = 100
}
```

</td></tr>
</tbody></table>

### تصريحات المتغيرات على المستوى العالي

على المستوى العالي، استخدم الكلمة الأساسية القياسية `var`. لا تحدد النوع، إلا إذا كان ليس نفس نوع التعبير.

<table>
<thead><tr><th>سيء</th><th>جيد</th></tr></thead>
<tbody>
<tr><td>

```go
var _s string = F()

func F() string { return "A" }
```

</td><td>

```go
var _s = F()
// بما أن F يعلن بالفعل أنه يعيد سلسلة، فلا داعي لتحديد
// النوع مرة أخرى.

func F() string { return "A" }
```

</td></tr>
</tbody></table>

حدد النوع إذا كان نوع التعبير لا يتطابق مع النوع المطلوب بالضبط.

```go
type myError struct{}

func (myError) Error() string { return "error" }

func F() myError { return myError{} }

var _e error = F()
// F يعيد كائن من نوع myError ولكننا نريد error.
```

### اضافة بادئة _ للمتغيرات العامة غير المصدرة

أضف بادئة `_` إلى `var`s و `const`s العالمية غير المصدرة لتوضيح متى يتم استخدامها أنها رموز عالمية.

المنطق: المتغيرات والثوابت على المستوى العالي لها نطاق الحزمة. استخدام اسم عام يجعل من السهل استخدام القيمة الخاطئة عن طريق الخطأ في ملف مختلف.

<table>
<thead><tr><th>سيء</th><th>جيد</th></tr></thead>
<tbody>
<tr><td>

```go
// foo.go

const (
  defaultPort = 8080
  defaultUser = "user"
)

// bar.go

func Bar() {
  defaultPort := 9090
  ...
  fmt.Println("Default port", defaultPort)

  // لن نرى خطأ في التجميع إذا تم حذف السطر الأول من
  // Bar()
}
```

</td><td>

```go
// foo.go

const (
  _defaultPort = 8080
  _defaultUser = "user"
)
```

</td></tr>
</tbody></table>

**استثناء**: قيم الأخطاء غير المصدرة يمكن أن تستخدم البادئة `err` بدون الشرطة السفلية.
انظر [تسمية الأخطاء](#تسمية-الأخطاء).

### التضمين في الهياكل

يجب أن تكون الأنواع المضمنة في أعلى قائمة حقول الهيكل، ويجب أن يكون هناك سطر فارغ يفصل الحقول المضمنة عن الحقول العادية.

<table>
<thead><tr><th>سيء</th><th>جيد</th></tr></thead>
<tbody>
<tr><td>

```go
type Client struct {
  version int
  http.Client
}
```

</td><td>

```go
type Client struct {
  http.Client

  version int
}
```

</td></tr>
</tbody></table>

يجب أن يوفر التضمين فائدة ملموسة، مثل إضافة أو زيادة الوظائف بطريقة مناسبة دلالياً. يجب أن يقوم بذلك مع عدم وجود أي تأثيرات سلبية تواجه المستخدم (انظر أيضًا: [تجنب تضمين الأنواع في الهياكل العامة](https://www.google.com/url?sa=E&source=gmail&q=#تجنب-تضمين-الأنواع-في-الهياكل-العامة)).

استثناء: لا ينبغي تضمين Mutexes، حتى في الأنواع غير المصدرة. انظر أيضًا: [تعتبر قيمة Mutex الصفرية صالحة](https://www.google.com/url?sa=E&source=gmail&q=#تعتبر-قيمة-mutex-الصفرية-صالحة).

**لا ينبغي** أن يكون التضمين:

  - تجميليًا بحتًا أو موجهًا للراحة.
  - يجعل الأنواع الخارجية أكثر صعوبة في الإنشاء أو الاستخدام.
  - يؤثر على قيم الصفر للأنواع الخارجية. إذا كان للنوع الخارجي قيمة صفرية مفيدة، فيجب أن يظل له قيمة صفرية مفيدة بعد تضمين النوع الداخلي.
  - يكشف عن دوال أو حقول غير ذات صلة من النوع الخارجي كتأثير جانبي لتضمين النوع الداخلي.
  - يكشف عن أنواع غير مصدرة.
  - يؤثر على دلالات نسخ الأنواع الخارجية.
  - يغير API النوع الخارجي أو دلالات النوع.
  - يضمن شكلًا غير أساسي من النوع الداخلي.
  - يكشف عن تفاصيل تنفيذ النوع الخارجي.
  - يسمح للمستخدمين بمراقبة أو التحكم في الأجزاء الداخلية للنوع.
  - يغير السلوك العام للدوال الداخلية من خلال التفاف بطريقة قد تفاجئ المستخدمين بشكل معقول.

ببساطة، قم بالتضمين بوعي وتعمد. اختبار جيد هو، "هل سيتم إضافة كل هذه الطرق / الحقول الداخلية المصدرة مباشرة إلى النوع الخارجي"؛ إذا كانت الإجابة "بعض" أو "لا"، فلا تقم بتضمين النوع الداخلي - استخدم حقلًا بدلاً من ذلك.

<table>
<thead><tr><th>سيء</th><th>جيد</th></tr></thead>
<tbody>
<tr><td>

```go
type A struct {
    // سيء: A.Lock() و A.Unlock() متاحان الآن،
    // لا يوفران أي فائدة وظيفية، ويسمحان
    // للمستخدمين بالتحكم في تفاصيل حول
    // الأجزاء الداخلية لـ A.
    sync.Mutex
}
```

</td><td>

```go
type countingWriteCloser struct {
    // جيد: يتم توفير Write() في هذه
    // الطبقة الخارجية لغرض محدد،
    // ويفوض العمل إلى Write()
    // للنوع الداخلي.
    io.WriteCloser

    count int
}

func (w *countingWriteCloser) Write(bsbyte) (int, error) {
    w.count += len(bs)
    return w.WriteCloser.Write(bs)
}
```

</td></tr>
<tr><td>

```go
type Book struct {
    // سيء: المؤشر يغير فائدة قيمة الصفر
    io.ReadWriter

    // حقول أخرى
}

// لاحقًا

var b Book
b.Read(...)  // panic: nil pointer
b.String()  // panic: nil pointer
b.Write(...) // panic: nil pointer
```

</td><td>

```go
type Book struct {
    // جيد: له قيمة صفرية مفيدة
    bytes.Buffer

    // حقول أخرى
}

// لاحقًا

var b Book
b.Read(...)  // ok
b.String()  // ok
b.Write(...) // ok
```

</td></tr>
<tr><td>

```go
type Client struct {
    sync.Mutex
    sync.WaitGroup
    bytes.Buffer
    url.URL
}
```

</td><td>

```go
type Client struct {
    mtx sync.Mutex
    wg  sync.WaitGroup
    buf bytes.Buffer
    url url.URL
}
```

</td></tr>
</tbody></table>

## تصريحات المتغيرات المحلية

يجب استخدام تصريحات المتغيرات القصيرة (`:=`) إذا تم تعيين متغير إلى قيمة ما بشكل صريح.

<table>
<thead><tr><th>سيء</th><th>جيد</th></tr></thead>
<tbody>
<tr><td>

```go
var s = "foo"
```

</td><td>

```go
s := "foo"
```

</td></tr>
</tbody></table>

ومع ذلك، هناك حالات تكون فيها القيمة الافتراضية أوضح عند استخدام كلمة `var` الرئيسية. [إعلان Slices فارغة](https://go.dev/wiki/CodeReviewComments#declaring-empty-slices)، على سبيل المثال.

<table>
<thead><tr><th>سيء</th><th>جيد</th></tr></thead>
<tbody>
<tr><td>

```go
func f(listint) {
    filtered :=int{}
    for _, v := range list {
        if v > 10 {
            filtered = append(filtered, v)
        }
    }
}
```

</td><td>

```go
func f(listint) {
    var filteredint
    for _, v := range list {
        if v > 10 {
            filtered = append(filtered, v)
        }
    }
}
```

</td></tr>
</tbody></table>

## nil هي قيمة صالحة لـ slice

`nil` هي قيمة صالحة لـ slice بطول 0. هذا يعني أنه،

  - لا يجب عليك إرجاع slice بطول صفر بشكل صريح. قم بإرجاع `nil` بدلاً من ذلك.

    <table>
    <thead><tr><th>سيء</th><th>جيد</th></tr></thead>
    <tbody>
    <tr><td>

    ```go
    if x == "" {
       returnint{}
    }
    ```

    </td><td>

    ```go
    if x == "" {
       return nil
    }
    ```

    </td></tr>
    </tbody></table>

  - للتحقق مما إذا كان slice فارغًا، استخدم دائمًا `len(s) == 0`. لا تتحقق من `nil`.

    <table>
    <thead><tr><th>سيء</th><th>جيد</th></tr></thead>
    <tbody>
    <tr><td>

    ```go
    func isEmpty(sstring) bool {
       return s == nil
    }
    ```

    </td><td>

    ```go
    func isEmpty(sstring) bool {
       return len(s) == 0
    }
    ```

    </td></tr>
    </tbody></table>

  - يمكن استخدام القيمة الصفرية (slice معلن باستخدام `var`) على الفور دون `make()`.

    <table>
    <thead><tr><th>سيء</th><th>جيد</th></tr></thead>
    <tbody>
    <tr><td>

    ```go
    nums :=int{}
    // أو، nums := make(int)

    if add1 {
       nums = append(nums, 1)
    }

    if add2 {
       nums = append(nums, 2)
    }
    ```

    </td><td>

    ```go
    var numsint

    if add1 {
       nums = append(nums, 1)
    }

    if add2 {
       nums = append(nums, 2)
    }
    ```

    </td></tr>
    </tbody></table>

تذكر أنه على الرغم من أنها slice صالحة، إلا أن slice nil لا تعادل slice مخصصة بطول 0 - أحدهما nil والآخر ليس كذلك - وقد يتم التعامل مع الاثنين بشكل مختلف في مواقف مختلفة (مثل التسلسل).

## تقليل نطاق المتغيرات

حيثما أمكن، قلل من نطاق المتغيرات والثوابت. لا تقلل النطاق إذا كان يتعارض مع [تقليل التداخل](https://www.google.com/url?sa=E&source=gmail&q=#تقليل-التداخل).

<table>
<thead><tr><th>سيء</th><th>جيد</th></tr></thead>
<tbody>
<tr><td>

```go
err := os.WriteFile(name, data, 0644)
if err != nil {
 return err
}
```

</td><td>

```go
if err := os.WriteFile(name, data, 0644); err != nil {
 return err
}
```

</td></tr>
</tbody></table>

إذا كنت بحاجة إلى نتيجة استدعاء دالة خارج if، فلا يجب عليك محاولة تقليل النطاق.

<table>
<thead><tr><th>سيء</th><th>جيد</th></tr></thead>
<tbody>
<tr><td>

```go
if data, err := os.ReadFile(name); err == nil {
  err = cfg.Decode(data)
  if err != nil {
    return err
  }

  fmt.Println(cfg)
  return nil
} else {
  return err
}
```

</td><td>

```go
data, err := os.ReadFile(name)
if err != nil {
    return err
}

if err := cfg.Decode(data); err != nil {
  return err
}

fmt.Println(cfg)
return nil
```

</td></tr>
</tbody></table>

لا تحتاج الثوابت إلى أن تكون عامة إلا إذا تم استخدامها في دوال أو ملفات متعددة أو كانت جزءًا من عقد خارجي للحزمة.

<table>
<thead><tr><th>سيء</th><th>جيد</th></tr></thead>
<tbody>
<tr><td>

```go
const (
  _defaultPort = 8080
  _defaultUser = "user"
)

func Bar() {
  fmt.Println("Default port", _defaultPort)
}
```

</td><td>

```go
func Bar() {
  const (
    defaultPort = 8080
    defaultUser = "user"
  )
  fmt.Println("Default port", defaultPort)
}
```

</td></tr>
</tbody></table>

## تجنب المعاملات المجردة

يمكن أن تضر المعاملات المجردة في استدعاءات الدوال بقابلية القراءة. أضف تعليقات على غرار C (`/* ... */`) لأسماء المعاملات عندما لا يكون معناها واضحًا.

<table>
<thead><tr><th>سيء</th><th>جيد</th></tr></thead>
<tbody>
<tr><td>

```go
// func printInfo(name string, isLocal, done bool)

printInfo("foo", true, true)
```

</td><td>

```go
// func printInfo(name string, isLocal, done bool)

printInfo("foo", true /* isLocal */, true /* done */)
```

</td></tr>
</tbody></table>

الأفضل من ذلك، استبدل أنواع `bool` المجردة بأنواع مخصصة للحصول على رمز أكثر قابلية للقراءة وآمن من النوع. يسمح هذا بأكثر من حالتين فقط (صحيح / خطأ) لتلك المعلمة في المستقبل.

```go
type Region int

const (
  UnknownRegion Region = iota
  Local
)

type Status int

const (
  StatusReady Status = iota + 1
  StatusDone
  // ربما سيكون لدينا StatusInProgress في المستقبل.
)

func printInfo(name string, region Region, status Status)
```

## استخدام محارف النصوص الخام لتجنب الهروب

يدعم Go [محارف النصوص الخام](https://go.dev/ref/spec#raw_string_lit)، والتي يمكن أن تمتد على أسطر متعددة وتتضمن علامات اقتباس. استخدم هذه لتجنب السلاسل التي تم الهروب منها يدويًا والتي يصعب قراءتها.

<table>
<thead><tr><th>سيء</th><th>جيد</th></tr></thead>
<tbody>
<tr><td>

```go
wantError := "unknown name:\"test\""
```

</td><td>

```go
wantError := `unknown error:"test"`
```

</td></tr>
</tbody></table>

## تهيئة الهياكل

### استخدام أسماء الحقول لتهيئة الهياكل

يجب عليك دائمًا تحديد أسماء الحقول عند تهيئة الهياكل. يتم فرض ذلك الآن بواسطة [`go vet`]([[https://pkg.go.dev/cmd/vet](https://pkg.go.dev/cmd/vet)]\([https://pkg.go.dev/cmd/vet](https://pkg.go.dev/cmd/vet)\)).

<table>
<thead><tr><th>سيء</th><th>جيد</th></tr></thead>
<tbody>
<tr><td>

```go
k := User{"John", "Doe", true}
```

</td><td>

```go
k := User{
    FirstName: "John",
    LastName: "Doe",
    Admin: true,
}
```

</td></tr>
</tbody></table>

استثناء: *يجوز* حذف أسماء الحقول في جداول الاختبار عندما يكون هناك 3 حقول أو أقل.

```go
tests :=struct{
  op Operation
  want string
}{
  {Add, "add"},
  {Subtract, "subtract"},
}
```

### حذف حقول القيمة الصفرية في الهياكل

عند تهيئة الهياكل بأسماء الحقول، احذف الحقول التي لها قيم صفرية إلا إذا كانت توفر سياقًا ذا مغزى. بخلاف ذلك، دع Go يعين هذه إلى قيم صفرية تلقائيًا.

<table>
<thead><tr><th>سيء</th><th>جيد</th></tr></thead>
<tbody>
<tr><td>

```go
user := User{
  FirstName: "John",
  LastName: "Doe",
  MiddleName: "",
  Admin: false,
}
```

</td><td>

```go
user := User{
  FirstName: "John",
  LastName: "Doe",
}
```

</td></tr>
</tbody></table>

يساعد هذا في تقليل الضوضاء للقراء عن طريق حذف القيم الافتراضية في هذا السياق. يتم تحديد القيم ذات المغزى فقط.

قم بتضمين قيم صفرية حيث توفر أسماء الحقول سياقًا ذا مغزى. على سبيل المثال، يمكن أن تستفيد حالات الاختبار في [جداول الاختبار](https://www.google.com/url?sa=E&source=gmail&q=#جداول-الاختبار) من أسماء الحقول حتى عندما تكون قيمتها صفرية.

```go
tests :=struct{
  give string
  want int
}{
  {give: "0", want: 0},
  // ...
}
```

### استخدام `var` للهياكل ذات القيمة الصفرية

عند حذف جميع حقول الهيكل في إعلان، استخدم صيغة `var` لإعلان الهيكل.

<table>
<thead><tr><th>سيء</th><th>جيد</th></tr></thead>
<tbody>
<tr><td>

```go
user := User{}
```

</td><td>

```go
var user User
```

</td></tr>
</tbody></table>

يميز هذا الهياكل ذات القيمة الصفرية عن تلك التي تحتوي على حقول غير صفرية على غرار التمييز الذي تم إنشاؤه لـ [تهيئة الخريطة](https://www.google.com/url?sa=E&source=gmail&q=#تهيئة-الخرائط)، ويتطابق مع كيفية تفضيلنا لـ [إعلان Slices فارغة](https://go.dev/wiki/CodeReviewComments#declaring-empty-slices).

### تهيئة مراجع الهياكل

استخدم `&T{}` بدلاً من `new(T)` عند تهيئة مراجع الهياكل بحيث تكون متسقة مع تهيئة الهيكل.

<table>
<thead><tr><th>سيء</th><th>جيد</th></tr></thead>
<tbody>
<tr><td>

```go
sval := T{Name: "foo"}

// غير متسق
sptr := new(T)
sptr.Name = "bar"
```

</td><td>

```go
sval := T{Name: "foo"}

sptr := &T{Name: "bar"}
```

</td></tr>
</tbody></table>

## تهيئة Maps

يفضل استخدام `make(..)` للخرائط الفارغة والخرائط المأهولة برمجيًا. هذا يجعل تهيئة الخريطة مميزة بصريًا عن الإعلان، ويجعل من السهل إضافة تلميحات الحجم لاحقًا إذا كانت متوفرة.

<table>
<thead><tr><th>سيء</th><th>جيد</th></tr></thead>
<tbody>
<tr><td>

```go
var (
  // m1 آمن للقراءة والكتابة؛
  // m2 سوف panic على الكتابة.
  m1 = map[T1]T2{}
  m2 map[T1]T2
)
```

</td><td>

```go
var (
  // m1 آمن للقراءة والكتابة؛
  // m2 سوف panic على الكتابة.
  m1 = make(map[T1]T2)
  m2 map[T1]T2
)
```

</td></tr>
<tr><td>

الإعلان والتهيئة متشابهان بصريًا.

</td><td>

الإعلان والتهيئة متميزان بصريًا.

</td></tr>
</tbody></table>

حيثما أمكن، قم بتوفير تلميحات السعة عند تهيئة الخرائط باستخدام `make()`. انظر [تحديد تلميحات سعة الخريطة](https://www.google.com/url?sa=E&source=gmail&q=#تحديد-تلميحات-سعة-الخريطة) لمزيد من المعلومات.

من ناحية أخرى، إذا كانت الخريطة تحتوي على قائمة ثابتة من العناصر، فاستخدم محارف الخريطة لتهيئة الخريطة.

<table>
<thead><tr><th>سيء</th><th>جيد</th></tr></thead>
<tbody>
<tr><td>

```go
m := make(map[T1]T2, 3)
m[k1] = v1
m[k2] = v2
m[k3] = v3
```

</td><td>

```go
m := map[T1]T2{
  k1: v1,
  k2: v2,
  k3: v3,
}
```

</td></tr>
</tbody></table>

القاعدة الأساسية هي استخدام محارف الخريطة عند إضافة مجموعة ثابتة من العناصر في وقت التهيئة، وإلا استخدم `make` (وحدد تلميحًا للحجم إذا كان متوفرًا).

## سلاسل التنسيق خارج Printf

إذا قمت بإعلان سلاسل تنسيق لدوال نمط `Printf` خارج حرف سلسلة، فاجعلها قيمًا `const`.

يساعد هذا `go vet` على إجراء تحليل ثابت لسلسلة التنسيق.

<table>
<thead><tr><th>سيء</th><th>جيد</th></tr></thead>
<tbody>
<tr><td>

```go
msg := "unexpected values %v, %v\n"
fmt.Printf(msg, 1, 2)
```

</td><td>

```go
const msg = "unexpected values %v, %v\n"
fmt.Printf(msg, 1, 2)
```

</td></tr>
</tbody></table>


## تسمية دوال Printf

عند إعلان دالة بنمط `Printf`، تأكد من أن `go vet` يمكنه اكتشافها والتحقق من سلسلة التنسيق.

هذا يعني أنه يجب عليك استخدام أسماء دوال Printf المعرفة مسبقًا إن أمكن. سيتحقق `go vet` من هذه بشكل افتراضي. راجع [عائلة Printf](https://pkg.go.dev/cmd/vet#hdr-Printf_family) لمزيد من المعلومات.

إذا لم يكن استخدام الأسماء المعرفة مسبقًا خيارًا، فقم بإنهاء الاسم الذي تختاره بـ f: `Wrapf`، وليس `Wrap`. يمكن مطالبة `go vet` بالتحقق من أسماء Printf محددة ولكن يجب أن تنتهي بـ f.

```shell
go vet -printfuncs=wrapf,statusf
```

راجع أيضًا [go vet: التحقق من عائلة Printf](https://kuzminva.wordpress.com/2017/11/07/go-vet-printf-family-check/).

## الأنماط

### جداول الاختبار

يمكن أن تكون الاختبارات التي تعتمد على الجداول مع [الاختبارات الفرعية](https://go.dev/blog/subtests) نمطًا مفيدًا لكتابة الاختبارات لتجنب تكرار التعليمات البرمجية عندما تكون منطق الاختبار الأساسي متكررًا.

إذا كان نظام ما قيد الاختبار يحتاج إلى اختباره مقابل *ظروف متعددة* حيث تتغير أجزاء معينة من المدخلات والمخرجات، فيجب استخدام اختبار يعتمد على الجدول لتقليل التكرار وتحسين قابلية القراءة.

<table>
<thead><tr><th>سيء</th><th>جيد</th></tr></thead>
<tbody>
<tr><td>

```go
// func TestSplitHostPort(t *testing.T)

host, port, err := net.SplitHostPort("192.0.2.0:8000")
require.NoError(t, err)
assert.Equal(t, "192.0.2.0", host)
assert.Equal(t, "8000", port)

host, port, err = net.SplitHostPort("192.0.2.0:http")
require.NoError(t, err)
assert.Equal(t, "192.0.2.0", host)
assert.Equal(t, "http", port)

host, port, err = net.SplitHostPort(":8000")
require.NoError(t, err)
assert.Equal(t, "", host)
assert.Equal(t, "8000", port)

host, port, err = net.SplitHostPort("1:8")
require.NoError(t, err)
assert.Equal(t, "1", host)
assert.Equal(t, "8", port)
```

</td><td>

```go
// func TestSplitHostPort(t *testing.T)

tests :=struct{
  give     string
  wantHost string
  wantPort string
}{
  {
    give:     "192.0.2.0:8000",
    wantHost: "192.0.2.0",
    wantPort: "8000",
  },
  {
    give:     "192.0.2.0:http",
    wantHost: "192.0.2.0",
    wantPort: "http",
  },
  {
    give:     ":8000",
    wantHost: "",
    wantPort: "8000",
  },
  {
    give:     "1:8",
    wantHost: "1",
    wantPort: "8",
  },
}

for _, tt := range tests {
  t.Run(tt.give, func(t *testing.T) {
    host, port, err := net.SplitHostPort(tt.give)
    require.NoError(t, err)
    assert.Equal(t, tt.wantHost, host)
    assert.Equal(t, tt.wantPort, port)
  })
}
```

</td></tr>
</tbody></table>

تسهل جداول الاختبار إضافة سياق إلى رسائل الخطأ وتقليل منطق التكرار وإضافة حالات اختبار جديدة.

نتبع الاصطلاح الذي يشير إلى شريحة الهياكل باسم `tests` وكل حالة اختبار `tt`. علاوة على ذلك، نشجع على توضيح قيم الإدخال والإخراج لكل حالة اختبار باستخدام بادئات `give` و `want`.

```go
tests :=struct{
  give     string
  wantHost string
  wantPort string
}{
  // ...
}

for _, tt := range tests {
  // ...
}
```

#### تجنب التعقيد غير الضروري في اختبارات الجدول

يمكن أن تكون اختبارات الجدول صعبة القراءة والصيانة إذا كانت الاختبارات الفرعية تحتوي على تأكيدات شرطية أو منطق تفرع آخر. لا ينبغي استخدام اختبارات الجدول كلما كانت هناك حاجة إلى منطق معقد أو شرطي داخل الاختبارات الفرعية (أي منطق معقد داخل حلقة `for`).

تضر اختبارات الجدول الكبيرة والمعقدة بقابلية القراءة والصيانة لأن قارئي الاختبار قد يواجهون صعوبة في تصحيح أخطاء الاختبار التي تحدث.

يجب تقسيم اختبارات الجدول مثل هذه إلى إما جداول اختبار متعددة أو دوال `Test ...` فردية متعددة.

بعض المُثل التي يجب أن تهدف إليها هي:

* التركيز على أضيق وحدة سلوك
* تقليل "عمق الاختبار" إلى أدنى حد، وتجنب التأكيدات الشرطية (انظر أدناه)
* تأكد من استخدام جميع حقول الجدول في جميع الاختبارات
* تأكد من تشغيل جميع منطق الاختبار لجميع حالات الجدول

في هذا السياق، يعني "عمق الاختبار" "ضمن اختبار معين، عدد التأكيدات المتتالية التي تتطلب تأكيدات سابقة للتمسك بها" (على غرار تعقيد الدورة).
يعني وجود اختبارات "أقل عمقًا" وجود علاقات أقل بين التأكيدات، والأهم من ذلك، أن هذه التأكيدات أقل عرضة للشرط افتراضيًا.

بشكل ملموس، يمكن أن تصبح اختبارات الجدول مربكة وصعبة القراءة إذا كانت تستخدم مسارات تفرع متعددة (مثل `shouldError` و `expectCall` وما إلى ذلك)، أو تستخدم العديد من عبارات `if` لتوقعات وهمية محددة (مثل `shouldCallFoo`)، أو ضع الدوال داخل الجدول (مثل `setupMocks func (* FooMock)`).

ومع ذلك، عند اختبار السلوك الذي يتغير فقط بناءً على تغيير الإدخال، قد يكون من الأفضل تجميع الحالات المتشابهة معًا في اختبار جدول لتوضيح كيفية تغير السلوك بشكل أفضل عبر جميع المدخلات، بدلاً من تقسيم الوحدات المماثلة إلى اختبارات منفصلة وجعلها أكثر صعوبة للمقارنة والتباين.

إذا كان نص الاختبار قصيرًا ومباشرًا، فمن المقبول وجود مسار تفرع واحد لحالات النجاح مقابل الفشل مع حقل جدول مثل `shouldErr` لتحديد توقعات الخطأ.

<table>
<thead><tr><th>سيء</th><th>جيد</th></tr></thead>
<tbody>
<tr><td>

```go
func TestComplicatedTable(t *testing.T) {
  tests :=struct {
    give          string
    want          string
    wantErr       error
    shouldCallX   bool
    shouldCallY   bool
    giveXResponse string
    giveXErr       error
    giveYResponse string
    giveYErr       error
  }{
    // ...
  }

  for _, tt := range tests {
    t.Run(tt.give, func(t *testing.T) {
      // setup mocks
      ctrl := gomock.NewController(t)
      xMock := xmock.NewMockX(ctrl)
      if tt.shouldCallX {
        xMock.EXPECT().Call().Return(
          tt.giveXResponse, tt.giveXErr,
        )
      }
      yMock := ymock.NewMockY(ctrl)
      if tt.shouldCallY {
        yMock.EXPECT().Call().Return(
          tt.giveYResponse, tt.giveYErr,
        )
      }

      got, err := DoComplexThing(tt.give, xMock, yMock)

      // verify results
      if tt.wantErr != nil {
        require.EqualError(t, err, tt.wantErr)
        return
      }
      require.NoError(t, err)
      assert.Equal(t, want, got)
    })
  }
}
```

</td><td>

```go
func TestShouldCallX(t *testing.T) {
  // setup mocks
  ctrl := gomock.NewController(t)
  xMock := xmock.NewMockX(ctrl)
  xMock.EXPECT().Call().Return("XResponse", nil)

  yMock := ymock.NewMockY(ctrl)

  got, err := DoComplexThing("inputX", xMock, yMock)

  require.NoError(t, err)
  assert.Equal(t, "want", got)
}

func TestShouldCallYAndFail(t *testing.T) {
  // setup mocks
  ctrl := gomock.NewController(t)
  xMock := xmock.NewMockX(ctrl)

  yMock := ymock.NewMockY(ctrl)
  yMock.EXPECT().Call().Return("YResponse", nil)

  _, err := DoComplexThing("inputY", xMock, yMock)
  assert.EqualError(t, err, "Y failed")
}
```
</td></tr>
</tbody></table>

هذا التعقيد يجعل من الصعب تغيير وفهم وإثبات صحة الاختبار.

على الرغم من عدم وجود إرشادات صارمة، إلا أنه يجب أن تكون قابلية القراءة والصيانة دائمًا في الاعتبار عند اتخاذ القرار بين اختبارات الجدول مقابل الاختبارات المنفصلة لمدخلات / مخرجات متعددة لنظام ما.

#### اختبارات متوازية

يجب أن تحرص الاختبارات المتوازية، مثل بعض الحلقات المتخصصة (على سبيل المثال، تلك التي تفرخ goroutines أو تلتقط مراجع كجزء من نص الحلقة)، على تخصيص متغيرات الحلقة بشكل صريح ضمن نطاق الحلقة لضمان احتفاظها بالقيم المتوقعة.

```go
tests :=struct{
  give string
  // ...
}{
  // ...
}

for _, tt := range tests {
  tt := tt // لـ t.Parallel
  t.Run(tt.give, func(t *testing.T) {
    t.Parallel()
    // ...
  })
}
```

في المثال أعلاه، يجب علينا إعلان متغير `tt` ضمن نطاق تكرار الحلقة بسبب استخدام `t.Parallel ()` أدناه.
إذا لم نفعل ذلك، فستتلقى معظم أو كل الاختبارات قيمة غير متوقعة لـ `tt`، أو قيمة تتغير أثناء تشغيلها.

### الخيارات الوظيفية

الخيارات الوظيفية هي نمط تعلن فيه عن نوع `Option` مُبهم يسجل المعلومات في بعض الهياكل الداخلية. أنت تقبل عددًا متغيرًا من هذه الخيارات وتتصرف بناءً على المعلومات الكاملة المسجلة بواسطة الخيارات في الهيكل الداخلي.

استخدم هذا النمط للوسائط الاختيارية في المُنشئات وواجهات برمجة التطبيقات العامة الأخرى التي تتوقع أنك ستحتاج إلى توسيعها، خاصةً إذا كان لديك بالفعل ثلاث وسيطات أو أكثر في تلك الدوال.

<table>
<thead><tr><th>سيء</th><th>جيد</th></tr></thead>
<tbody>
<tr><td>

```go
// package db

func Open(
  addr string,
  cache bool,
  logger *zap.Logger
) (*Connection, error) {
  // ...
}
```

</td><td>

```go
// package db

type Option interface {
  // ...
}

func WithCache(c bool) Option {
  // ...
}

func WithLogger(log *zap.Logger) Option {
  // ...
}

// Open creates a connection.
func Open(
  addr string,
  opts ...Option,
) (*Connection, error) {
  // ...
}
```

</td></tr>
<tr><td>

يجب دائمًا توفير معلمات ذاكرة التخزين المؤقت والمسجل، حتى لو أراد المستخدم استخدام الإعداد الافتراضي.

```go
db.Open(addr, db.DefaultCache, zap.NewNop())
db.Open(addr, db.DefaultCache, log)
db.Open(addr, false /* cache */, zap.NewNop())
db.Open(addr, false /* cache */, log)
```

</td><td>

يتم توفير الخيارات فقط إذا لزم الأمر.

```go
db.Open(addr)
db.Open(addr, db.WithLogger(log))
db.Open(addr, db.WithCache(false))
db.Open(
  addr,
  db.WithCache(false),
  db.WithLogger(log),
)
```

</td></tr>
</tbody></table>

طريقتنا المقترحة لتنفيذ هذا النمط هي مع واجهة `Option` التي تحتوي على طريقة غير مُصدرة، وتسجيل الخيارات في بنية `options` غير مُصدرة.

```go
type options struct {
  cache  bool
  logger *zap.Logger
}

type Option interface {
  apply(*options)
}

type cacheOption bool

func (c cacheOption) apply(opts *options) {
  opts.cache = bool(c)
}

func WithCache(c bool) Option {
  return cacheOption(c)
}

type loggerOption struct {
  Log *zap.Logger
}

func (l loggerOption) apply(opts *options) {
  opts.logger = l.Log
}

func WithLogger(log *zap.Logger) Option {
  return loggerOption{Log: log}
}

// Open creates a connection.
func Open(
  addr string,
  opts ...Option,
) (*Connection, error) {
  options := options{
    cache:  defaultCache,
    logger: zap.NewNop(),
  }

  for _, o := range opts {
    o.apply(&options)
  }

  // ...
}
```

لاحظ أن هناك طريقة لتنفيذ هذا النمط باستخدام عمليات الإغلاق ولكننا نعتقد أن النمط أعلاه يوفر مرونة أكبر للمؤلفين وأسهل في التصحيح والاختبار للمستخدمين. على وجه الخصوص، يسمح بمقارنة الخيارات مع بعضها البعض في الاختبارات والمحاكاة، مقابل عمليات الإغلاق حيث يكون ذلك مستحيلًا. علاوة على ذلك، فإنه يتيح للخيارات تنفيذ واجهات أخرى، بما في ذلك `fmt.Stringer` الذي يسمح بتمثيلات سلسلة قابلة للقراءة من قبل المستخدم للخيارات.

راجع أيضًا،

- [الدوال المرجعية الذاتية وتصميم الخيارات](https://commandcenter.blogspot.com/2014/01/self-referential-functions-and-design.html)
- [الخيارات الوظيفية لواجهات برمجة التطبيقات سهلة الاستخدام](https://dave.cheney.net/2014/10/17/functional-options-for-friendly-apis)

## التدقيق اللغوي

الأهم من أي مجموعة "مباركة" من المدققات اللغوية، هو التدقيق اللغوي بشكل متسق عبر قاعدة التعليمات البرمجية.

نوصي باستخدام المدققات اللغوية التالية كحد أدنى، لأننا نشعر أنها تساعد في اكتشاف المشكلات الأكثر شيوعًا وأيضًا وضع معيار عالٍ لجودة التعليمات البرمجية دون أن تكون وصفية بشكل غير ضروري:

- [errcheck](https://github.com/kisielk/errcheck) للتأكد من معالجة الأخطاء
- [goimports](https://pkg.go.dev/golang.org/x/tools/cmd/goimports) لتنسيق التعليمات البرمجية وإدارة عمليات الاستيراد
- [golint](https://github.com/golang/lint) للإشارة إلى أخطاء الأسلوب الشائعة
- [govet](https://pkg.go.dev/cmd/vet) لتحليل التعليمات البرمجية بحثًا عن الأخطاء الشائعة
- [staticcheck](https://staticcheck.dev) لإجراء فحوصات تحليل ثابتة مختلفة

### Lint Runners

نوصي بـ [golangci-lint](https://github.com/golangci/golangci-lint) كأداة تشغيل lint للتعليمات البرمجية Go، ويرجع ذلك إلى حد كبير إلى أدائها في قواعد التعليمات البرمجية الأكبر وقدرتها على تكوين واستخدام العديد من أدوات lint الأساسية في وقت واحد. يحتوي هذا المستودع على مثال لملف التكوين [.golangci.yml](https://github.com/uber-go/guide/blob/master/.golangci.yml) مع أدوات lint وإعدادات موصى بها.

يحتوي golangci-lint على [أدوات lint متنوعة](https://golangci-lint.run/usage/linters/) متاحة