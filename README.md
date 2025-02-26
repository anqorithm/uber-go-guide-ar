# دليل أسلوب Uber للغة Go

- [المقدمة](#المقدمة)
- [الإرشادات](#الإرشادات)
  - [المؤشرات للواجهات](#المؤشرات-للواجهات)
  - [التحقق من الامتثال للواجهة](#التحقق-من-الامتثال-للواجهة)
  - [المستقبلات والواجهات](#المستقبلات-والواجهات)
  - [قيمة الصفر لـ Mutexes صالحة](#قيمة-الصفر-لـ-mutexes-صالحة)
  - [نسخ الشرائح والخرائط عند الحدود](#نسخ-الشرائح-والخرائط-عند-الحدود)
  - [استخدام Defer للتنظيف](#استخدام-defer-للتنظيف)
  - [حجم القناة واحد أو صفر](#حجم-القناة-واحد-أو-صفر)
  - [بدء التعدادات من واحد](#بدء-التعدادات-من-واحد)
  - [استخدام "time" للتعامل مع الوقت](#استخدام-time-للتعامل-مع-الوقت)
  - [الأخطاء](#الأخطاء)
    - [أنواع الأخطاء](#أنواع-الأخطاء)
    - [تغليف الأخطاء](#تغليف-الأخطاء)
    - [تسمية الأخطاء](#تسمية-الأخطاء)
    - [التعامل مع الأخطاء مرة واحدة](#التعامل-مع-الأخطاء-مرة-واحدة)
  - [التعامل مع فشل تأكيد النوع](#التعامل-مع-فشل-تأكيد-النوع)
  - [لا تستخدم Panic](#لا-تستخدم-panic)
  - [استخدام go.uber.org/atomic](#استخدام-gouberorgatomic)
  - [تجنب المتغيرات العالمية القابلة للتغيير](#تجنب-المتغيرات-العالمية-القابلة-للتغيير)
  - [تجنب تضمين الأنواع في الهياكل العامة](#تجنب-تضمين-الأنواع-في-الهياكل-العامة)
  - [تجنب استخدام الأسماء المضمنة](#تجنب-استخدام-الأسماء-المضمنة)
  - [تجنب استخدام init()](#تجنب-استخدام-init)
  - [الخروج في الدالة الرئيسية](#الخروج-في-الدالة-الرئيسية)
    - [الخروج مرة واحدة](#الخروج-مرة-واحدة)
  - [استخدام علامات الحقل في الهياكل المحولة](#استخدام-علامات-الحقل-في-الهياكل-المحولة)
  - [لا تطلق goroutines وتنساها](#لا-تطلق-goroutines-وتنساها)
    - [انتظر خروج goroutines](#انتظر-خروج-goroutines)
    - [لا تستخدم goroutines في init()](#لا-تستخدم-goroutines-في-init)
- [الأداء](#الأداء)
  - [تفضيل strconv على fmt](#تفضيل-strconv-على-fmt)
  - [تجنب تحويلات السلسلة إلى بايت المتكررة](#تجنب-تحويلات-السلسلة-إلى-بايت-المتكررة)
  - [تفضيل تحديد سعة الحاوية](#تفضيل-تحديد-سعة-الحاوية)
- [الأسلوب](#الأسلوب)
  - [تجنب الأسطر الطويلة جدًا](#تجنب-الأسطر-الطويلة-جدًا)
  - [كن متسقًا](#كن-متسقًا)
  - [تجميع التصريحات المماثلة](#تجميع-التصريحات-المماثلة)
  - [ترتيب مجموعات الاستيراد](#ترتيب-مجموعات-الاستيراد)
  - [أسماء الحزم](#أسماء-الحزم)
  - [أسماء الدوال](#أسماء-الدوال)
  - [استخدام الاسم المستعار للاستيراد](#استخدام-الاسم-المستعار-للاستيراد)
  - [تجميع وترتيب الدوال](#تجميع-وترتيب-الدوال)
  - [تقليل التداخل](#تقليل-التداخل)
  - [Else غير الضرورية](#else-غير-الضرورية)
  - [إعلانات المتغيرات على المستوى الأعلى](#إعلانات-المتغيرات-على-المستوى-الأعلى)
  - [إضافة بادئة _ للمتغيرات العالمية غير المصدرة](#إضافة-بادئة-_-للمتغيرات-العالمية-غير-المصدرة)
  - [التضمين في الهياكل](#التضمين-في-الهياكل)
  - [إعلانات المتغيرات المحلية](#إعلانات-المتغيرات-المحلية)
  - [nil هي شريحة صالحة](#nil-هي-شريحة-صالحة)
  - [تقليل نطاق المتغيرات](#تقليل-نطاق-المتغيرات)
  - [تجنب المعلمات المجردة](#تجنب-المعلمات-المجردة)
  - [استخدام الحروف المباشرة لتجنب التهرب](#استخدام-الحروف-المباشرة-لتجنب-التهرب)
  - [تهيئة الهياكل](#تهيئة-الهياكل)
    - [استخدام أسماء الحقول لتهيئة الهياكل](#استخدام-أسماء-الحقول-لتهيئة-الهياكل)
    - [حذف حقول القيمة الصفرية في الهياكل](#حذف-حقول-القيمة-الصفرية-في-الهياكل)
    - [استخدام var للهياكل ذات القيمة الصفرية](#استخدام-var-للهياكل-ذات-القيمة-الصفرية)
    - [تهيئة مراجع الهياكل](#تهيئة-مراجع-الهياكل)
  - [تهيئة الخرائط](#تهيئة-الخرائط)
  - [تنسيق السلاسل خارج Printf](#تنسيق-السلاسل-خارج-printf)
  - [تسمية دوال نمط Printf](#تسمية-دوال-نمط-printf)
- [الأنماط](#الأنماط)
  - [جداول الاختبار](#جداول-الاختبار)
  - [الخيارات الوظيفية](#الخيارات-الوظيفية)
- [Linting](#linting)

## المقدمة

الأساليب هي الاتفاقيات التي تحكم الكود الخاص بنا. مصطلح "الأسلوب" قد يكون مضللًا بعض الشيء، لأن هذه الاتفاقيات تغطي أكثر بكثير من مجرد تنسيق ملف المصدر - يتعامل gofmt مع ذلك نيابة عنا.

هدف هذا الدليل هو إدارة هذا التعقيد من خلال وصف لما يجب وما لا يجب فعله عند كتابة كود Go في Uber. هذه القواعد موجودة للحفاظ على قاعدة الكود قابلة للإدارة مع السماح للمهندسين باستخدام ميزات لغة Go بشكل منتج.

تم إنشاء هذا الدليل في الأصل بواسطة [Prashant Varanasi](https://github.com/prashantv) و [Simon Newton](https://github.com/nomis52) كوسيلة لمساعدة بعض الزملاء على التعامل مع استخدام Go. على مر السنين، تم تعديله بناءً على ملاحظات من الآخرين.

تصف هذه الوثيقة الاتفاقيات المتعارف عليها في كود Go التي نتبعها في Uber. الكثير من هذه الإرشادات عامة لـ Go، بينما يعتمد البعض الآخر على موارد خارجية:

1. [Effective Go](https://go.dev/doc/effective_go)
2. [Go Common Mistakes](https://go.dev/wiki/CommonMistakes)
3. [Go Code Review Comments](https://go.dev/wiki/CodeReviewComments)

نهدف لأن تكون عينات الكود دقيقة لأحدث إصدارين فرعيين من [إصدارات](https://go.dev/doc/devel/release) Go.

يجب أن يكون كل الكود خاليًا من الأخطاء عند تشغيله من خلال `golint` و `go vet`. نوصي بإعداد المحرر الخاص بك لـ:

- تشغيل `goimports` عند الحفظ
- تشغيل `golint` و `go vet` للتحقق من الأخطاء

يمكنك العثور على معلومات حول دعم المحرر لأدوات Go هنا:
https://go.dev/wiki/IDEsAndTextEditorPlugins

## الإرشادات

### المؤشرات للواجهات

نادرًا ما تحتاج إلى مؤشر لواجهة. يجب أن تمرر الواجهات كقيم - البيانات الأساسية يمكن أن تظل مؤشرًا.

الواجهة عبارة عن حقلين:

1. مؤشر إلى بعض المعلومات الخاصة بالنوع. يمكنك التفكير في هذا على أنه "النوع".
2. مؤشر البيانات. إذا كانت البيانات المخزنة عبارة عن مؤشر، فيتم تخزينها مباشرة. إذا كانت البيانات المخزنة قيمة، فيتم تخزين مؤشر إلى القيمة.

إذا كنت تريد من طرق الواجهة تعديل البيانات الأساسية، فيجب استخدام مؤشر.

### التحقق من الامتثال للواجهة

تحقق من امتثال الواجهة في وقت التجميع عند الاقتضاء. وهذا يشمل:

- الأنواع المصدرة التي يجب أن تنفذ واجهات محددة كجزء من عقد API الخاص بها
- الأنواع المصدرة أو غير المصدرة التي هي جزء من مجموعة من الأنواع التي تنفذ نفس الواجهة
- حالات أخرى حيث انتهاك واجهة قد يضر بالمستخدمين

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

العبارة `var _ http.Handler = (*Handler)(nil)` ستفشل في التجميع إذا توقف `*Handler` عن مطابقة واجهة `http.Handler`.

الجانب الأيمن من التعيين يجب أن يكون القيمة الصفرية للنوع المؤكد. هذا هو `nil` لأنواع المؤشرات (مثل `*Handler`) والشرائح والخرائط، وهيكل فارغ لأنواع الهياكل.

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

يمكن استدعاء الطرق ذات مستقبلات القيمة على المؤشرات وكذلك القيم. لا يمكن استدعاء الطرق ذات مستقبلات المؤشر إلا على المؤشرات أو [القيم القابلة للعنونة](https://go.dev/ref/spec#Method_values).

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

// لا يمكننا الحصول على مؤشرات للقيم المخزنة في الخرائط، لأنها ليست
// قيمًا قابلة للعنونة.
sVals := map[int]S{1: {"A"}}

// يمكننا استدعاء Read على القيم المخزنة في الخريطة لأن Read
// لديها مستقبل قيمة، والذي لا يتطلب من القيمة أن تكون
// قابلة للعنونة.
sVals[1].Read()

// لا يمكننا استدعاء Write على القيم المخزنة في الخريطة لأن Write
// لديها مستقبل مؤشر، ومن غير الممكن الحصول على مؤشر
// لقيمة مخزنة في خريطة.
//
//  sVals[1].Write("test")

sPtrs := map[int]*S{1: {"A"}}

// يمكنك استدعاء كل من Read و Write إذا كانت الخريطة تخزن مؤشرات،
// لأن المؤشرات قابلة للعنونة بشكل ذاتي.
sPtrs[1].Read()
sPtrs[1].Write("test")
```

وبالمثل، يمكن إرضاء واجهة بواسطة مؤشر، حتى إذا كان للطريقة مستقبل قيمة.

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

// ما يلي لا يتم تجميعه، لأن s2Val هي قيمة، ولا يوجد مستقبل قيمة لـ f.
//   i = s2Val
```

Effective Go لديها شرح جيد حول [المؤشرات مقابل القيم](https://go.dev/doc/effective_go#pointers_vs_values).

### قيمة الصفر لـ Mutexes صالحة

القيمة الصفرية لـ `sync.Mutex` و `sync.RWMutex` صالحة، لذلك نادرًا ما تحتاج إلى مؤشر إلى mutex.

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

إذا كنت تستخدم هيكلًا بواسطة مؤشر، فيجب أن يكون mutex حقلًا غير مؤشر عليه. لا تقم بتضمين mutex في الهيكل، حتى لو كان الهيكل غير مصدر.

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

الحقل `Mutex`، والطرق `Lock` و `Unlock` هي جزء غير مقصود من API المصدرة لـ `SMap`.

</td><td>

الـ mutex وطرقه هي تفاصيل تنفيذ لـ `SMap` مخفية عن مستدعيها.

</td></tr>
</tbody></table>

### نسخ الشرائح والخرائط عند الحدود

تحتوي الشرائح والخرائط على مؤشرات إلى البيانات الأساسية، لذا كن حذرًا من السيناريوهات التي تحتاج فيها إلى نسخها.

#### استلام الشرائح والخرائط

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

// هل قصدت تعديل d1.trips؟
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

#### إرجاع الشرائح والخرائط

وبالمثل، كن حذرًا من تعديلات المستخدمين للخرائط أو الشرائح التي تكشف عن الحالة الداخلية.

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

// لم تعد لقطة محمية بواسطة mutex، لذلك أي
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

// الآن Snapshot هي نسخة.
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

// من السهل نسيان فتح الأقفال بسبب العودات المتعددة
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

// أكثر قابلية للقراءة
```

</td></tr>
</tbody></table>

استخدام Defer لديه تكلفة صغيرة جدًا ويجب تجنبه فقط إذا استطعت إثبات أن وقت تنفيذ الدالة الخاصة بك في حدود النانوثانية. الفائدة التي تحصل عليها من حيث سهولة القراءة عند استخدام defer تستحق التكلفة الضئيلة لاستخدامها. وهذا صحيح بشكل خاص للدوال الكبيرة التي تتضمن أكثر من مجرد عمليات وصول بسيطة للذاكرة، حيث تكون العمليات الحسابية الأخرى أكثر أهمية من استخدام `defer`.

### حجم القناة واحد أو صفر

يجب أن يكون للقنوات عادةً حجم واحد أو أن تكون غير مخزنة مؤقتًا. بشكل افتراضي، تكون القنوات غير مخزنة مؤقتًا وحجمها صفر. يجب أن يخضع أي حجم آخر لمستوى عالٍ من التدقيق. فكر في كيفية تحديد الحجم، وما الذي يمنع القناة من الامتلاء تحت الحمل وحظر الكتّاب، وماذا يحدث عندما يحدث هذا.

<table>
<thead><tr><th>سيء</th><th>جيد</th></tr></thead>
<tbody>
<tr><td>

```go
// يجب أن تكون كافية لأي شخص!
c := make(chan int, 64)
```

</td><td>

```go
// حجم واحد
c := make(chan int, 1) // أو
// قناة غير مخزنة مؤقتًا، حجم صفر
c := make(chan int)
```

</td></tr>
</tbody></table>

### بدء التعدادات من واحد

الطريقة القياسية لتقديم التعدادات في Go هي إعلان نوع مخصص ومجموعة `const` مع `iota`. بما أن المتغيرات لها قيمة افتراضية 0، فيجب عليك عادةً أن تبدأ التعدادات الخاصة بك بقيمة غير صفرية.

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

هناك حالات يكون فيها استخدام القيمة الصفرية منطقيًا، على سبيل المثال عندما تكون حالة القيمة الصفرية هي السلوك الافتراضي المرغوب.

```go
type LogOutput int

const (
  LogToStdout LogOutput = iota
  LogToFile
  LogToRemote
)

// LogToStdout=0, LogToFile=1, LogToRemote=2
```

<!-- TODO: section on String methods for enums -->

### استخدام "time" للتعامل مع الوقت

الوقت معقد. الافتراضات الخاطئة التي غالبًا ما تتم حول الوقت تشمل ما يلي.

1. اليوم يحتوي على 24 ساعة
2. الساعة تحتوي على 60 دقيقة
3. الأسبوع يحتوي على 7 أيام
4. السنة تحتوي على 365 يومًا
5. [والكثير غير ذلك](https://infiniteundo.com/post/25326999628/falsehoods-programmers-believe-about-time)

على سبيل المثال، *1* يعني أن إضافة 24 ساعة إلى لحظة زمنية لن تؤدي دائمًا إلى يوم تقويمي جديد.

لذلك، استخدم دائمًا حزمة [`"time"`](https://pkg.go.dev/time) عند التعامل مع الوقت لأنها تساعد في التعامل مع هذه الافتراضات الخاطئة بطريقة أكثر أمانًا ودقة.

#### استخدم `time.Time` للحظات الزمنية

استخدم [`time.Time`](https://pkg.go.dev/time#Time) عند التعامل مع لحظات زمنية، واستخدم الدوال الموجودة في `time.Time` عند المقارنة أو الإضافة أو الطرح للوقت.

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

#### استخدم `time.Duration` لفترات زمنية

استخدم [`time.Duration`](https://pkg.go.dev/time#Duration) عند التعامل مع فترات زمنية.

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

بالعودة إلى مثال إضافة 24 ساعة إلى لحظة زمنية، تعتمد الطريقة التي نستخدمها لإضافة الوقت على النية. إذا أردنا نفس الوقت من اليوم، ولكن في اليوم التقويمي التالي، فيجب استخدام [`Time.AddDate`](https://pkg.go.dev/time#Time.AddDate). ومع ذلك، إذا أردنا لحظة زمنية مضمونة أن تكون بعد 24 ساعة من الوقت السابق، فيجب استخدام [`Time.Add`](https://pkg.go.dev/time#Time.Add).

```go
newDay := t.AddDate(0 /* years */, 0 /* months */, 1 /* days */)
maybeNewDay := t.Add(24 * time.Hour)
```

#### استخدم `time.Time` و `time.Duration` مع الأنظمة الخارجية

استخدم `time.Duration` و `time.Time` في التفاعلات مع الأنظمة الخارجية عندما يكون ذلك ممكنًا. على سبيل المثال:

- علامات سطر الأوامر: [`flag`](https://pkg.go.dev/flag) يدعم `time.Duration` من خلال [`time.ParseDuration`](https://pkg.go.dev/time#ParseDuration)
- JSON: [`encoding/json`](https://pkg.go.dev/encoding/json) يدعم ترميز `time.Time` كسلسلة [RFC 3339](https://tools.ietf.org/html/rfc3339) من خلال دالة [`UnmarshalJSON`](https://pkg.go.dev/time#Time.UnmarshalJSON)
- SQL: [`database/sql`](https://pkg.go.dev/database/sql) يدعم تحويل أعمدة `DATETIME` أو `TIMESTAMP` إلى `time.Time` والعكس إذا كان برنامج التشغيل الأساسي يدعم ذلك
- YAML: [`gopkg.in/yaml.v2`](https://pkg.go.dev/gopkg.in/yaml.v2) يدعم `time.Time` كسلسلة [RFC 3339](https://tools.ietf.org/html/rfc3339)، و `time.Duration` من خلال [`time.ParseDuration`](https://pkg.go.dev/time#ParseDuration).

عندما لا يكون من الممكن استخدام `time.Duration` في هذه التفاعلات، استخدم `int` أو `float64` وضمّن الوحدة في اسم الحقل.

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

عندما لا يكون من الممكن استخدام `time.Time` في هذه التفاعلات، ما لم يتم الاتفاق على بديل، استخدم `string` وقم بتنسيق الطوابع الزمنية كما هو محدد في [RFC 3339](https://tools.ietf.org/html/rfc3339). يتم استخدام هذا التنسيق افتراضيًا بواسطة [`Time.UnmarshalText`](https://pkg.go.dev/time#Time.UnmarshalText) وهو متاح للاستخدام في `Time.Format` و `time.Parse` من خلال [`time.RFC3339`](https://pkg.go.dev/time#RFC3339).

على الرغم من أن هذا لا يميل إلى أن يكون مشكلة في الممارسة العملية، ضع في اعتبارك أن حزمة `"time"` لا تدعم تحليل الطوابع الزمنية مع الثواني الكبيسة ([8728](https://github.com/golang/go/issues/8728))، ولا تأخذ في الاعتبار الثواني الكبيسة في الحسابات ([15190](https://github.com/golang/go/issues/15190)). إذا قارنت بين لحظتين زمنيتين، فإن الفرق لن يتضمن الثواني الكبيسة التي قد تكون حدثت بين هاتين اللحظتين.

### الأخطاء

#### أنواع الأخطاء

هناك عدة خيارات لإعلان الأخطاء.
فكر في ما يلي قبل اختيار الخيار الأنسب لحالة الاستخدام الخاصة بك.

- هل يحتاج المستدعي إلى مطابقة الخطأ حتى يتمكن من التعامل معه؟
  إذا كانت الإجابة بنعم، فيجب علينا دعم دوال [`errors.Is`](https://pkg.go.dev/errors#Is) أو [`errors.As`](https://pkg.go.dev/errors#As)
  من خلال إعلان متغير خطأ على المستوى الأعلى أو نوع مخصص.
- هل رسالة الخطأ سلسلة ثابتة،
  أم أنها سلسلة ديناميكية تتطلب معلومات سياقية؟
  بالنسبة للأولى، يمكننا استخدام [`errors.New`](https://pkg.go.dev/errors#New)، ولكن بالنسبة للأخيرة يجب علينا استخدام [`fmt.Errorf`](https://pkg.go.dev/fmt#Errorf) أو نوع خطأ مخصص.
- هل نقوم بنشر خطأ جديد أعادته دالة تابعة؟
  إذا كان الأمر كذلك، راجع [قسم تغليف الأخطاء](#تغليف-الأخطاء).

| مطابقة الخطأ؟ | رسالة الخطأ | التوجيه                                                          |
|---------------|-------------|------------------------------------------------------------------|
| لا            | ثابتة       | [`errors.New`](https://pkg.go.dev/errors#New)                     |
| لا            | ديناميكية   | [`fmt.Errorf`](https://pkg.go.dev/fmt#Errorf)                     |
| نعم           | ثابتة       | متغير `var` على المستوى الأعلى مع [`errors.New`](https://pkg.go.dev/errors#New) |
| نعم           | ديناميكية   | نوع `error` مخصص                                                |

على سبيل المثال،
استخدم [`errors.New`](https://pkg.go.dev/errors#New) للخطأ ذو السلسلة الثابتة.
قم بتصدير هذا الخطأ كمتغير لدعم مطابقته مع `errors.Is`
إذا كان المستدعي بحاجة إلى مطابقة ومعالجة هذا الخطأ.

<table>
<thead><tr><th>بدون مطابقة الخطأ</th><th>مع مطابقة الخطأ</th></tr></thead>
<tbody>
<tr><td>

```go
// حزمة foo

func Open() error {
  return errors.New("could not open")
}

// حزمة bar

if err := foo.Open(); err != nil {
  // لا يمكن التعامل مع الخطأ.
  panic("unknown error")
}
```

</td><td>

```go
// حزمة foo

var ErrCouldNotOpen = errors.New("could not open")

func Open() error {
  return ErrCouldNotOpen
}

// حزمة bar

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

بالنسبة للخطأ ذو السلسلة الديناميكية،
استخدم [`fmt.Errorf`](https://pkg.go.dev/fmt#Errorf) إذا لم يكن المستدعي بحاجة إلى مطابقته،
ونوع `error` مخصص إذا كان المستدعي بحاجة إلى مطابقته.

<table>
<thead><tr><th>بدون مطابقة الخطأ</th><th>مع مطابقة الخطأ</th></tr></thead>
<tbody>
<tr><td>

```go
// حزمة foo

func Open(file string) error {
  return fmt.Errorf("file %q not found", file)
}

// حزمة bar

if err := foo.Open("testfile.txt"); err != nil {
  // لا يمكن التعامل مع الخطأ.
  panic("unknown error")
}
```

</td><td>

```go
// حزمة foo

type NotFoundError struct {
  File string
}

func (e *NotFoundError) Error() string {
  return fmt.Sprintf("file %q not found", e.File)
}

func Open(file string) error {
  return &NotFoundError{File: file}
}


// حزمة bar

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

لاحظ أنه إذا قمت بتصدير متغيرات أخطاء أو أنواع من حزمة،
فستصبح جزءًا من واجهة API العامة للحزمة.

#### تغليف الأخطاء

هناك ثلاثة خيارات رئيسية لنشر الأخطاء إذا فشل الاستدعاء:

- إرجاع الخطأ الأصلي كما هو
- إضافة سياق باستخدام `fmt.Errorf` والفعل `%w`
- إضافة سياق باستخدام `fmt.Errorf` والفعل `%v`

أعد الخطأ الأصلي كما هو إذا لم يكن هناك سياق إضافي لإضافته.
هذا يحافظ على نوع الخطأ الأصلي والرسالة.
هذا مناسب للحالات التي تحتوي فيها رسالة الخطأ الأساسية
على معلومات كافية لتتبع مصدرها.

وإلا، أضف سياقًا إلى رسالة الخطأ حيثما أمكن
بحيث بدلاً من خطأ غامض مثل "الاتصال مرفوض"،
تحصل على أخطاء أكثر فائدة مثل "استدعاء الخدمة foo: الاتصال مرفوض".

استخدم `fmt.Errorf` لإضافة سياق إلى الأخطاء الخاصة بك،
واختر بين الأفعال `%w` أو `%v`
بناءً على ما إذا كان يجب أن يكون المستدعي قادرًا على
مطابقة واستخراج السبب الأساسي.

- استخدم `%w` إذا كان يجب أن يتمكن المستدعي من الوصول إلى الخطأ الأساسي.
  هذا هو الخيار الافتراضي الجيد لمعظم الأخطاء المغلفة،
  ولكن كن على دراية بأن المستدعين قد يبدأون في الاعتماد على هذا السلوك.
  لذلك بالنسبة للحالات التي يكون فيها الخطأ المغلف هو متغير `var` معروف أو نوع،
  قم بتوثيقه واختباره كجزء من عقد الدالة الخاصة بك.
- استخدم `%v` لإخفاء الخطأ الأساسي.
  لن يتمكن المستدعون من مطابقته،
  ولكن يمكنك التبديل إلى `%w` في المستقبل إذا لزم الأمر.

عند إضافة سياق إلى الأخطاء المرتجعة، حافظ على السياق موجزًا من خلال تجنب
عبارات مثل "فشل في"، والتي تذكر الواضح وتتراكم مع تصاعد الخطأ
عبر المكدس:

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

ومع ذلك، بمجرد إرسال الخطأ إلى نظام آخر، يجب أن يكون واضحًا أن
الرسالة هي خطأ (مثل علامة `err` أو بادئة "Failed" في السجلات).

انظر أيضًا [لا تتحقق فقط من الأخطاء، تعامل معها بأناقة](https://dave.cheney.net/2016/04/27/dont-just-check-errors-handle-them-gracefully).

#### تسمية الأخطاء

بالنسبة لقيم الخطأ المخزنة كمتغيرات عالمية،
استخدم البادئة `Err` أو `err` اعتمادًا على ما إذا كانت مصدرة.
هذا التوجيه يحل محل [إضافة بادئة _ للمتغيرات العالمية غير المصدرة](#إضافة-بادئة-_-للمتغيرات-العالمية-غير-المصدرة).

```go
var (
  // الخطآن التاليان يتم تصديرهما
  // بحيث يمكن لمستخدمي هذه الحزمة مطابقتهما
  // مع errors.Is.

  ErrBrokenLink = errors.New("link is broken")
  ErrCouldNotOpen = errors.New("could not open")

  // هذا الخطأ غير مصدر لأننا
  // لا نريد جعله جزءًا من واجهة API العامة.
  // قد نستخدمه داخل الحزمة
  // مع errors.Is.

  errNotFound = errors.New("not found")
)
```

بالنسبة لأنواع الأخطاء المخصصة، استخدم اللاحقة `Error` بدلاً من ذلك.

```go
// وبالمثل، يتم تصدير هذا الخطأ
// بحيث يمكن لمستخدمي هذه الحزمة مطابقته
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

عندما يتلقى المستدعي خطأ من دالة مستدعاة،
يمكنه التعامل معه بمجموعة متنوعة من الطرق المختلفة
اعتمادًا على ما يعرفه عن الخطأ.

وتشمل هذه، على سبيل المثال لا الحصر:

- إذا كان عقد الدالة المستدعاة يحدد أخطاء محددة،
  مطابقة الخطأ مع `errors.Is` أو `errors.As`
  والتعامل مع الفروع بشكل مختلف
- إذا كان الخطأ قابلاً للاستعادة،
  تسجيل الخطأ وتدهور الأداء بشكل لطيف
- إذا كان الخطأ يمثل حالة فشل خاصة بالمجال،
  إرجاع خطأ محدد جيدًا
- إرجاع الخطأ، إما [مغلفًا](#تغليف-الأخطاء) أو حرفيًا

بغض النظر عن كيفية تعامل المستدعي مع الخطأ،
يجب عليه عادةً التعامل مع كل خطأ مرة واحدة فقط.
لا ينبغي للمستدعي، على سبيل المثال، تسجيل الخطأ ثم إرجاعه،
لأن مستدعياته قد تتعامل مع الخطأ أيضًا.

على سبيل المثال، انظر في الحالات التالية:

<table>
<thead><tr><th>الوصف</th><th>الكود</th></tr></thead>
<tbody>
<tr><td>

**سيء**: تسجيل الخطأ وإرجاعه

من المحتمل أن يتخذ المستدعون في أعلى المكدس إجراءً مماثلاً مع الخطأ.
القيام بذلك يتسبب في الكثير من الضوضاء في سجلات التطبيق مقابل قيمة قليلة.

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

**جيد**: تغليف الخطأ وإرجاعه

سيتعامل المستدعون في أعلى المكدس مع الخطأ.
استخدام `%w` يضمن أنهم يستطيعون مطابقة الخطأ مع `errors.Is` أو `errors.As`
إذا كان ذلك ملائمًا.

</td><td>

```go
u, err := getUser(id)
if err != nil {
  return fmt.Errorf("get user %q: %w", id, err)
}
```

</td></tr>
<tr><td>

**جيد**: تسجيل الخطأ وتدهور الأداء بشكل لطيف

إذا لم تكن العملية ضرورية بشكل صارم،
يمكننا توفير تجربة متدهورة ولكن غير مكسورة
من خلال التعافي منها.

</td><td>

```go
if err := emitMetrics(); err != nil {
  // الفشل في كتابة المقاييس يجب
  // ألا يُعطل التطبيق.
  log.Printf("Could not emit metrics: %v", err)
}

```

</td></tr>
<tr><td>

**جيد**: مطابقة الخطأ وتدهور الأداء بشكل لطيف

إذا كانت الدالة المستدعاة تحدد خطأ معين في عقدها،
وكان الفشل قابل للاستعادة،
قم بمطابقة حالة الخطأ هذه وتدهور الأداء بشكل لطيف.
بالنسبة لجميع الحالات الأخرى، قم بتغليف الخطأ وإرجاعه.

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

الشكل ذو القيمة المرتجعة الواحدة لـ [تأكيد النوع](https://go.dev/ref/spec#Type_assertions) سيتسبب في حدوث panic عند استخدام نوع غير صحيح. لذلك، استخدم دائمًا تعبير "الفاصلة ok".

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
  // التعامل مع الخطأ بلطف
}
```

</td></tr>
</tbody></table>

<!-- TODO: There are a few situations where the single assignment form is
fine. -->

### لا تستخدم Panic

يجب أن يتجنب الكود الذي يعمل في الإنتاج استخدام panic. Panics هي مصدر رئيسي لـ
[الأعطال المتتالية](https://en.wikipedia.org/wiki/Cascading_failure). إذا حدث خطأ، يجب أن ترجع الدالة خطأً
وتدع المستدعي يقرر كيفية التعامل معه.

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

Panic/recover ليست استراتيجية للتعامل مع الأخطاء. يجب أن يحدث panic في البرنامج فقط عندما
يحدث شيء لا يمكن استعادته مثل إشارة nil. استثناء لهذا هو
تهيئة البرنامج: الأشياء السيئة عند بدء تشغيل البرنامج التي يجب أن توقف
البرنامج قد تسبب panic.

```go
var _statusTemplate = template.Must(template.New("name").Parse("_statusHTML"))
```

حتى في الاختبارات، فضّل استخدام `t.Fatal` أو `t.FailNow` على panics للتأكد من أن
الاختبار معلّم على أنه فشل.

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

### استخدام go.uber.org/atomic

العمليات الذرية مع حزمة [sync/atomic](https://pkg.go.dev/sync/atomic) تعمل على الأنواع الأولية
(`int32`، `int64`، إلخ) لذلك من السهل نسيان استخدام العملية الذرية لـ
قراءة أو تعديل المتغيرات.

[go.uber.org/atomic](https://pkg.go.dev/go.uber.org/atomic) يضيف أمان النوع إلى هذه العمليات من خلال إخفاء
النوع الأساسي. بالإضافة إلى ذلك، فإنه يتضمن نوع `atomic.Bool` المناسب.

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
     // يعمل بالفعل…
     return
  }
  // بدء تشغيل Foo
}

func (f *foo) isRunning() bool {
  return f.running == 1  // تنافس!
}
```

</td><td>

```go
type foo struct {
  running atomic.Bool
}

func (f *foo) start() {
  if f.running.Swap(true) {
     // يعمل بالفعل…
     return
  }
  // بدء تشغيل Foo
}

func (f *foo) isRunning() bool {
  return f.running.Load()
}
```

</td></tr>
</tbody></table>

### تجنب المتغيرات العالمية القابلة للتغيير

تجنب تغيير المتغيرات العالمية، واختر بدلاً من ذلك حقن التبعية.
ينطبق هذا على مؤشرات الدوال وكذلك على أنواع أخرى من القيم.

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

### تجنب تضمين الأنواع في الهياكل العامة

هذه الأنواع المضمنة تسرب تفاصيل التنفيذ، وتمنع تطور النوع، و
تخفي التوثيق.

بافتراض أنك قمت بتنفيذ مجموعة متنوعة من أنواع القوائم باستخدام
`AbstractList` مشترك، تجنب تضمين `AbstractList` في تنفيذات القائمة
الملموسة الخاصة بك.
بدلاً من ذلك، اكتب يدويًا فقط الدوال للقائمة الملموسة التي ستفوض
إلى القائمة المجردة.

```go
type AbstractList struct {}

// Add يضيف كيانًا إلى القائمة.
func (l *AbstractList) Add(e Entity) {
  // ...
}

// Remove يزيل كيانًا من القائمة.
func (l *AbstractList) Remove(e Entity) {
  // ...
}
```

<table>
<thead><tr><th>سيء</th><th>جيد</th></tr></thead>
<tbody>
<tr><td>

```go
// ConcreteList هي قائمة من الكيانات.
type ConcreteList struct {
  *AbstractList
}
```

</td><td>

```go
// ConcreteList هي قائمة من الكيانات.
type ConcreteList struct {
  list *AbstractList
}

// Add يضيف كيانًا إلى القائمة.
func (l *ConcreteList) Add(e Entity) {
  l.list.Add(e)
}

// Remove يزيل كيانًا من القائمة.
func (l *ConcreteList) Remove(e Entity) {
  l.list.Remove(e)
}
```

</td></tr>
</tbody></table>

Go يسمح بـ [تضمين النوع](https://go.dev/doc/effective_go#embedding) كتسوية بين الوراثة والتركيب.
يحصل النوع الخارجي على نسخ ضمنية من دوال النوع المضمن.
هذه الدوال، بشكل افتراضي، تفوض إلى نفس الدالة للمثيل
المضمن.

يكتسب الهيكل أيضًا حقلًا بنفس اسم النوع.
لذلك، إذا كان النوع المضمن عامًا، فإن الحقل يكون عامًا.
للحفاظ على التوافق الخلفي، يجب على كل إصدار مستقبلي من النوع الخارجي
الاحتفاظ بالنوع المضمن.

النوع المضمن نادرًا ما يكون ضروريًا.
إنها وسيلة راحة تساعدك على تجنب كتابة دوال التفويض المملة.

حتى تضمين واجهة AbstractList *واجهة* متوافقة، بدلاً من الهيكل،
من شأنه أن يوفر للمطور مرونة أكبر للتغيير في المستقبل، ولكنه لا يزال
يسرب التفاصيل أن القوائم الملموسة تستخدم تنفيذًا مجردًا.

<table>
<thead><tr><th>سيء</th><th>جيد</th></tr></thead>
<tbody>
<tr><td>

```go
// AbstractList هو تنفيذ معمم
// لأنواع مختلفة من قوائم الكيانات.
type AbstractList interface {
  Add(Entity)
  Remove(Entity)
}

// ConcreteList هي قائمة من الكيانات.
type ConcreteList struct {
  AbstractList
}
```

</td><td>

```go
// ConcreteList هي قائمة من الكيانات.
type ConcreteList struct {
  list AbstractList
}

// Add يضيف كيانًا إلى القائمة.
func (l *ConcreteList) Add(e Entity) {
  l.list.Add(e)
}

// Remove يزيل كيانًا من القائمة.
func (l *ConcreteList) Remove(e Entity) {
  l.list.Remove(e)
}
```

</td></tr>
</tbody></table>

سواء مع هيكل مضمن أو واجهة مضمنة، فإن النوع المضمن
يضع قيودًا على تطور النوع.

- إضافة دوال إلى واجهة مضمنة يعد تغييرًا كسريًا.
- إزالة دوال من هيكل مضمن يعد تغييرًا كسريًا.
- إزالة النوع المضمن يعد تغييرًا كسريًا.
- استبدال النوع المضمن، حتى مع بديل يلبي نفس
  الواجهة، يعد تغييرًا كسريًا.

على الرغم من أن كتابة دوال التفويض هذه أمر مُمل، إلا أن الجهد الإضافي يخفي
تفاصيل التنفيذ، ويترك المزيد من الفرص للتغيير، وأيضًا
يزيل التوجيه غير المباشر لاكتشاف واجهة List الكاملة في
التوثيق.

### تجنب استخدام الأسماء المضمنة

[مواصفات لغة](https://go.dev/ref/spec) Go تحدد العديد من المعرفات المضمنة،
[المعرفات المعلنة مسبقًا](https://go.dev/ref/spec#Predeclared_identifiers) التي لا ينبغي استخدامها كأسماء داخل برامج Go.

اعتمادًا على السياق، فإن إعادة استخدام هذه المعرفات كأسماء سيؤدي إما إلى تظليل
الأصل داخل النطاق المعجمي الحالي (وأي نطاقات متداخلة) أو جعل
الكود المتأثر مربكًا. في أفضل الحالات، سيشتكي المُصرِّف؛ في
أسوأ الحالات، قد يُدخل مثل هذا الكود أخطاء كامنة يصعب تتبعها.

<table>
<thead><tr><th>سيء</th><th>جيد</th></tr></thead>
<tbody>
<tr><td>

```go
var error string
// `error` تظلل المضمن

// أو

func handleErrorMessage(error string) {
    // `error` تظلل المضمن
}
```

</td><td>

```go
var errorMessage string
// `error` تشير إلى المضمن

// أو

func handleErrorMessage(msg string) {
    // `error` تشير إلى المضمن
}
```

</td></tr>
<tr><td>

```go
type Foo struct {
    // بينما هذه الحقول تقنيًا لا
    // تشكل تظليلًا، فإن البحث عن
    // سلاسل `error` أو `string` أصبح الآن
    // غامضًا.
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
    // سلاسل `error` و `string` 
    // الآن غير غامضة.
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

لاحظ أن المُصرِّف لن يولد أخطاء عند استخدام المعرفات المعلنة
مسبقًا، ولكن أدوات مثل `go vet` يجب أن تشير بشكل صحيح إلى هذه و
حالات أخرى من التظليل.

### تجنب استخدام init()

تجنب استخدام `init()` حيثما أمكن ذلك. عندما يكون استخدام `init()` أمرًا لا مفر منه أو مرغوبًا فيه، يجب أن يحاول الكود:

1. أن يكون حتميًا تمامًا، بغض النظر عن بيئة البرنامج أو طريقة الاستدعاء.
2. تجنب الاعتماد على ترتيب أو الآثار الجانبية لدوال `init()` أخرى.
   على الرغم من أن ترتيب `init()` معروف جيدًا، إلا أن الكود يمكن أن يتغير، وبالتالي
   يمكن أن تجعل العلاقات بين دوال `init()` الكود هشًا وعرضة للأخطاء.
3. تجنب الوصول إلى حالة عالمية أو بيئية أو معالجتها، مثل
   معلومات الجهاز، متغيرات البيئة، دليل العمل، برنامج
   الوسائط/المدخلات، إلخ.
4. تجنب عمليات الإدخال/الإخراج، بما في ذلك كل من نظام الملفات والشبكة واستدعاءات النظام.

الكود الذي لا يمكنه تلبية هذه المتطلبات ينتمي على الأرجح كمساعد ليتم
استدعاؤه كجزء من `main()` (أو في مكان آخر في دورة حياة البرنامج)، أو أن يتم
كتابته كجزء من `main()` نفسها. على وجه الخصوص، يجب أن تأخذ المكتبات المخصصة
للاستخدام من قبل برامج أخرى عناية خاصة لتكون حتمية تماماً ولا تقوم بـ "سحر init".

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

// أو، بشكل أفضل، للاختبار:

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

بالنظر إلى ما سبق، بعض الحالات التي قد يكون فيها استخدام `init()` مفضلاً أو
ضروريًا قد تشمل:

- التعبيرات المعقدة التي لا يمكن تمثيلها كتعيينات مفردة.
- خطافات قابلة للتوصيل، مثل لهجات `database/sql`، سجلات أنواع الترميز، إلخ.
- تحسينات لـ [Google Cloud Functions](https://cloud.google.com/functions/docs/bestpractices/tips#use_global_variables_to_reuse_objects_in_future_invocations) وأشكال أخرى من الحساب المسبق الحتمي.

### الخروج في الدالة الرئيسية

تستخدم برامج Go [`os.Exit`](https://pkg.go.dev/os#Exit) أو [`log.Fatal*`](https://pkg.go.dev/log#Fatal) للخروج فورًا. (الـ Panicking
ليست طريقة جيدة للخروج من البرامج، الرجاء [عدم استخدام panic](#لا-تستخدم-panic).)

قم باستدعاء واحدة من `os.Exit` أو `log.Fatal*` **فقط في `main()`**. يجب أن تعيد جميع الدوال
الأخرى أخطاء للإشارة إلى الفشل.

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

المنطق: البرامج ذات الدوال المتعددة التي تخرج تقدم بعض المشكلات:

- تدفق التحكم غير الواضح: يمكن لأي دالة أن تخرج من البرنامج لذلك يصبح
  من الصعب فهم تدفق التحكم.
- صعوبة الاختبار: الدالة التي تخرج من البرنامج ستخرج أيضًا من الاختبار
  الذي يستدعيها. هذا يجعل من الصعب اختبار الدالة ويقدم خطر
  تخطي اختبارات أخرى لم يتم تشغيلها بعد بواسطة `go test`.
- تخطي التنظيف: عندما تخرج دالة من البرنامج، فإنها تتخطى استدعاءات الدالة
  المضافة إلى قائمة الانتظار مع عبارات `defer`. هذا يضيف خطر تخطي مهام
  تنظيف مهمة.

#### الخروج مرة واحدة

إذا كان ذلك ممكنًا، فضّل استدعاء `os.Exit` أو `log.Fatal` **مرة واحدة على الأكثر** في
دالة `main()` الخاصة بك. إذا كانت هناك سيناريوهات خطأ متعددة تؤدي إلى إيقاف تنفيذ البرنامج،
ضع هذا المنطق تحت دالة منفصلة وأعد الأخطاء منها.

هذا له تأثير تقصير دالة `main()` الخاصة بك ووضع كل منطق العمل الرئيسي في دالة
منفصلة قابلة للاختبار.

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

المثال أعلاه يستخدم `log.Fatal`، لكن التوجيه ينطبق أيضًا على
`os.Exit` أو أي كود مكتبة يستدعي `os.Exit`.

```go
func main() {
  if err := run(); err != nil {
    fmt.Fprintln(os.Stderr, err)
    os.Exit(1)
  }
}
```

يمكنك تعديل توقيع `run()` ليناسب احتياجاتك.
على سبيل المثال، إذا كان برنامجك يجب أن يخرج برموز خروج محددة للإخفاقات،
فقد تعيد `run()` رمز الخروج بدلاً من خطأ.
هذا يسمح لاختبارات الوحدة بالتحقق من هذا السلوك مباشرة أيضًا.

```go
func main() {
  os.Exit(run(args))
}

func run() (exitCode int) {
  // ...
}
```

بشكل أكثر عمومية، لاحظ أن دالة `run()` المستخدمة في هذه الأمثلة
ليست مقصودة لتكون إرشادية.
هناك مرونة في الاسم والتوقيع وإعداد دالة `run()`.
من بين أمور أخرى، قد:

- تقبل وسائط سطر الأوامر غير المحللة (مثل `run(os.Args[1:])`)
- تحلل وسائط سطر الأوامر في `main()` وتمررها إلى `run`
- تستخدم نوع خطأ مخصص لحمل رمز الخروج مرة أخرى إلى `main()`
- تضع منطق العمل في طبقة مختلفة من التجريد من `package main`

هذا التوجيه يتطلب فقط أن يكون هناك مكان واحد في `main()` الخاص بك
مسؤول عن الخروج الفعلي من العملية.

### استخدام علامات الحقل في الهياكل المحولة

أي حقل هيكل يتم تحويله إلى JSON أو YAML
أو تنسيقات أخرى تدعم تسمية الحقول المستندة إلى العلامات
يجب أن يتم التعليق عليه بالعلامة ذات الصلة.

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

المنطق:
النموذج المسلسل للهيكل هو عقد بين أنظمة مختلفة.
التغييرات في بنية النموذج المسلسل - بما في ذلك أسماء الحقول - تكسر
هذا العقد. تحديد أسماء الحقول داخل العلامات يجعل العقد صريحًا،
ويحمي من كسر العقد عن طريق الخطأ من خلال إعادة هيكلة أو
إعادة تسمية الحقول.

### لا تطلق goroutines وتنساها

Goroutines خفيفة، لكنها ليست مجانية:
على الأقل، تكلف ذاكرة لمكدسها ووحدة المعالجة المركزية ليتم جدولتها.
في حين أن هذه التكاليف صغيرة للاستخدامات النموذجية لـ goroutines،
يمكن أن تسبب مشاكل أداء كبيرة
عند إطلاقها بأعداد كبيرة بدون فترات حياة محكومة.
يمكن أن تتسبب goroutines ذات فترات الحياة غير المدارة أيضًا في مشاكل أخرى
مثل منع الكائنات غير المستخدمة من أن يتم جمعها كنفايات
والاحتفاظ بالموارد التي لم تعد مستخدمة.

لذلك، لا تسمح بتسرب goroutines في كود الإنتاج.
استخدم [go.uber.org/goleak](https://pkg.go.dev/go.uber.org/goleak)
لاختبار تسربات goroutine داخل الحزم التي قد تطلق goroutines.

بشكل عام، كل goroutine:

- يجب أن يكون لها وقت متوقع ستتوقف فيه عن التشغيل؛ أو
- يجب أن تكون هناك طريقة للإشارة إلى goroutine بأنه يجب أن تتوقف

في كلتا الحالتين، يجب أن تكون هناك طريقة للكود لحظر وانتظار goroutine
لتنتهي.

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
  stop = make(chan struct{}) // يخبر goroutine بالتوقف
  done = make(chan struct{}) // يخبرنا أن goroutine خرجت
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
close(stop)  // الإشارة إلى goroutine للتوقف
<-done       // والانتظار حتى تخرج
```

</td></tr>
<tr><td>

لا توجد طريقة لإيقاف هذه الـ goroutine.
ستعمل هذه حتى يخرج التطبيق.

</td><td>

يمكن إيقاف هذه الـ goroutine بـ `close(stop)`،
ويمكننا انتظارها للخروج مع `<-done`.

</td></tr>
</tbody></table>

#### انتظر خروج goroutines

مع وجود goroutine تم إطلاقها بواسطة النظام،
يجب أن تكون هناك طريقة للانتظار حتى تخرج الـ goroutine.
هناك طريقتان شائعتان للقيام بذلك:

- استخدم `sync.WaitGroup`.
  افعل هذا إذا كان هناك goroutines متعددة تريد انتظارها

  ```go
  var wg sync.WaitGroup
  for i := 0; i < N; i++ {
    wg.Add(1)
    go func() {
      defer wg.Done()
      // ...
    }()
  }

  // للانتظار حتى ينتهي الجميع:
  wg.Wait()
  ```

- أضف `chan struct{}` آخر تغلقه goroutine عندما تنتهي.
  افعل هذا إذا كان هناك goroutine واحدة فقط.

  ```go
  done := make(chan struct{})
  go func() {
    defer close(done)
    // ...
  }()

  // للانتظار حتى تنتهي goroutine:
  <-done
  ```

#### لا تستخدم goroutines في `init()`

يجب ألا تطلق دوال `init()` goroutines.
انظر أيضًا [تجنب init()](#تجنب-استخدام-init).

إذا كانت هناك حاجة لحزمة إلى goroutine خلفية،
فيجب أن تعرض كائنًا مسؤولاً عن إدارة فترة حياة
goroutine.
يجب أن يوفر الكائن طريقة (`Close`، `Stop`، `Shutdown`، إلخ)
التي تشير إلى goroutine الخلفية للتوقف، وتنتظر حتى تخرج.

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

تطلق goroutine خلفية بشكل غير مشروط عندما يقوم المستخدم بتصدير هذه الحزمة.
ليس لدى المستخدم أي تحكم في goroutine أو وسيلة لإيقافها.

</td><td>

تطلق العامل فقط إذا طلبه المستخدم.
توفر وسيلة لإيقاف العامل حتى يتمكن المستخدم من تحرير
الموارد التي يستخدمها العامل.

لاحظ أنه يجب عليك استخدام `WaitGroup`s إذا كان العامل يدير goroutines
متعددة.
انظر [انتظر خروج goroutines](#انتظر-خروج-goroutines).

</td></tr>
</tbody></table>

## الأداء

الإرشادات الخاصة بالأداء تنطبق فقط على المسار الساخن (hot path).

### تفضيل strconv على fmt

عند تحويل الأنواع الأولية من/إلى سلاسل، `strconv` أسرع من
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

### تجنب تحويلات السلسلة إلى بايت المتكررة

لا تقم بإنشاء شرائح بايت من سلسلة ثابتة بشكل متكرر. بدلاً من ذلك، قم بالتحويل
مرة واحدة واحتفظ بالنتيجة.

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

### تفضيل تحديد سعة الحاوية

حدد سعة الحاوية حيثما أمكن من أجل تخصيص الذاكرة للحاوية
مسبقًا. هذا يقلل من التخصيصات اللاحقة (عن طريق نسخ وتغيير حجم
الحاوية) عند إضافة العناصر.

#### تحديد تلميحات سعة الخريطة

حيثما أمكن، قدم تلميحات السعة عند تهيئة
الخرائط باستخدام `make()`.

```go
make(map[T1]T2, hint)
```

توفير تلميح السعة إلى `make()` يحاول تحديد حجم الخريطة بشكل صحيح في
وقت التهيئة، مما يقلل من الحاجة إلى توسيع الخريطة والتخصيصات
حيث يتم إضافة العناصر إلى الخريطة.

لاحظ أنه، على عكس الشرائح، تلميحات سعة الخريطة لا تضمن تخصيصًا كاملاً
مسبقًا، ولكنها تستخدم لتقريب عدد دلاء خريطة التجزئة
المطلوبة. وبالتالي، قد تحدث تخصيصات عند إضافة عناصر إلى
الخريطة، حتى السعة المحددة.

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

تم إنشاء `m` بدون تلميح للحجم؛ قد يكون هناك المزيد
من التخصيصات في وقت التعيين.

</td><td>

تم إنشاء `m` مع تلميح للحجم؛ قد يكون هناك تخصيصات أقل
في وقت التعيين.

</td></tr>
</tbody></table>

#### تحديد سعة الشريحة

حيثما أمكن، قدم تلميحات السعة عند تهيئة الشرائح باستخدام `make()`،
خاصة عند الإلحاق.

```go
make([]T, length, capacity)
```

على عكس الخرائط، سعة الشريحة ليست مجرد تلميح: سيخصص المترجم ذاكرة كافية
لسعة الشريحة كما تم توفيرها إلى `make()`، مما يعني أن
عمليات `append()` اللاحقة لن تتطلب أي تخصيصات (حتى يتطابق طول
الشريحة مع السعة، وبعد ذلك ستتطلب أي عمليات إلحاق إعادة تحجيم
لاحتواء عناصر إضافية).

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

تجنب أسطر الكود التي تتطلب من القراء التمرير أفقيًا
أو إدارة رؤوسهم كثيرًا.

نوصي بحد طول لين للسطر هو **99 حرفًا**.
يجب أن يهدف المؤلفون إلى لف الأسطر قبل الوصول إلى هذا الحد،
لكنه ليس حدًا صارمًا.
يُسمح للكود بتجاوز هذا الحد.

### كن متسقًا

يمكن تقييم بعض الإرشادات الموضحة في هذه الوثيقة بشكل موضوعي؛
البعض الآخر ظرفي أو سياقي أو ذاتي.

فوق كل شيء آخر، **كن متسقًا**.

الكود المتسق أسهل في الصيانة، وأسهل في التبرير، ويتطلب جهدًا ذهنيًا أقل،
وأسهل في الترحيل أو التحديث مع ظهور اتفاقيات جديدة
أو إصلاح فئات من الأخطاء.

على العكس من ذلك، وجود أساليب متباينة أو متضاربة داخل قاعدة كود واحدة
يتسبب في عبء صيانة، وعدم يقين، وتنافر معرفي،
كل ذلك يمكن أن يسهم مباشرة في انخفاض السرعة، ومراجعات الكود المؤلمة،
والأخطاء.

عند تطبيق هذه الإرشادات على قاعدة كود، يوصى بإجراء التغييرات
على مستوى الحزمة (أو أكبر): التطبيق على مستوى الحزمة الفرعية
ينتهك الاهتمام المذكور أعلاه من خلال إدخال أساليب متعددة في نفس الكود.

### تجميع التصريحات المماثلة

تدعم Go تجميع التصريحات المماثلة.

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

وهذا ينطبق أيضًا على الثوابت والمتغيرات وإعلانات النوع.

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

قم بتجميع التصريحات ذات الصلة فقط. لا تجمع التصريحات التي ليست ذات صلة.

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

المجموعات غير محدودة في المكان الذي يمكن استخدامها فيه. على سبيل المثال، يمكنك استخدامها
داخل الدوال.

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

استثناء: يجب تجميع إعلانات المتغيرات، خاصة داخل الدوال،
معًا إذا تم إعلانها بجوار متغيرات أخرى. افعل هذا للمتغيرات
المعلنة معًا حتى لو كانت غير مرتبطة.

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

- مكتبة قياسية
- كل شيء آخر

هذا هو التجميع الذي يطبقه goimports بشكل افتراضي.

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

عند تسمية الحزم، اختر اسمًا:

- كله بأحرف صغيرة. لا توجد أحرف كبيرة أو شرطات سفلية.
- لا يحتاج إلى إعادة تسمية باستخدام الاستيرادات المسماة في معظم مواقع الاستدعاء.
- قصير وموجز. تذكر أن الاسم يتم تحديده بالكامل في كل موقع استدعاء.
- ليس بصيغة الجمع. على سبيل المثال، `net/url`، وليس `net/urls`.
- ليس "common" أو "util" أو "shared" أو "lib". هذه أسماء سيئة وغير مفيدة.

انظر أيضًا [Package Names](https://go.dev/blog/package-names) و [Style guideline for Go packages](https://rakyll.org/style-packages/).

### أسماء الدوال

نتبع اتفاقية مجتمع Go باستخدام [MixedCaps لأسماء الدوال](https://go.dev/doc/effective_go#mixed-caps). يتم عمل استثناء لدوال الاختبار، التي قد تحتوي على شرطات سفلية لغرض تجميع حالات الاختبار ذات الصلة، على سبيل المثال،
`TestMyFunction_WhatIsBeingTested`.

### استخدام الأسماء المستعارة (Aliasing) في الاستيرادات

يجب استخدام الأسماء المستعارة للاستيراد إذا كان اسم الحزمة لا يتطابق مع العنصر الأخير من مسار الاستيراد.

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

- يجب ترتيب الدوال حسب ترتيب الاستدعاء التقريبي.
- يجب تجميع الدوال في ملف حسب المستقبل (receiver).

لذلك، يجب أن تظهر الدوال المصدرة أولاً في الملف، بعد تعريفات `struct` و `const` و `var`.

يمكن أن تظهر دالة `newXYZ()`/`NewXYZ()` بعد تعريف النوع، ولكن قبل بقية الطرق على المستقبل.

بما أن الدوال مجمعة حسب المستقبل، فإن الدوال المساعدة البسيطة يجب أن تظهر نحو نهاية الملف.

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

يجب أن يقلل الكود من التداخل حيثما أمكن عن طريق معالجة حالات الخطأ/الحالات الخاصة أولاً والعودة مبكرًا أو مواصلة الحلقة. قلل من كمية الكود المتداخل على مستويات متعددة.

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

### العبارات الشرطية (Else) غير الضرورية

إذا تم تعيين متغير في كلا فرعي عبارة if، يمكن استبداله بعبارة if واحدة.

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

### إعلانات المتغيرات على المستوى الأعلى

في المستوى الأعلى، استخدم الكلمة المفتاحية القياسية `var`. لا تحدد النوع، إلا إذا كان ليس نفس نوع التعبير.

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
// نظرًا لأن F تنص بالفعل على أنها تعيد سلسلة، فلا نحتاج إلى تحديد
// النوع مرة أخرى.

func F() string { return "A" }
```

</td></tr>
</tbody></table>

حدد النوع إذا كان نوع التعبير لا يتطابق تمامًا مع النوع المطلوب.

```go
type myError struct{}

func (myError) Error() string { return "error" }

func F() myError { return myError{} }

var _e error = F()
// F تعيد كائنًا من نوع myError ولكننا نريد error.
```

### استخدم بادئة _ للمتغيرات العامة غير المصدرة

ضع بادئة `_` للمتغيرات والثوابت العامة غير المصدرة `var` و `const` لتوضيح متى يتم استخدامها أنها رموز عامة.

المنطق: المتغيرات والثوابت ذات المستوى الأعلى لها نطاق الحزمة. استخدام اسم عام يجعل من السهل استخدام القيمة الخاطئة عن طريق الخطأ في ملف مختلف.

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

**استثناء**: يمكن لقيم الخطأ غير المصدرة استخدام البادئة `err` بدون الشرطة السفلية.
انظر [تسمية الأخطاء](#error-naming).

### التضمين في الهياكل

يجب أن تكون الأنواع المضمنة في أعلى قائمة الحقول للهيكل، ويجب أن يكون هناك سطر فارغ يفصل بين الحقول المضمنة والحقول العادية.

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

يجب أن يوفر التضمين فائدة ملموسة، مثل إضافة أو تعزيز الوظائف بطريقة مناسبة دلاليًا. يجب أن يفعل ذلك بدون آثار جانبية سلبية على المستخدم (انظر أيضًا: [تجنب تضمين الأنواع في الهياكل العامة](#avoid-embedding-types-in-public-structs)).

استثناء: لا ينبغي تضمين المتغيرات المتزامنة (Mutexes)، حتى على الأنواع غير المصدرة. انظر أيضًا: [المتغيرات المتزامنة ذات القيمة الصفرية صالحة](#zero-value-mutexes-are-valid).

التضمين **لا ينبغي**:

- أن يكون لأغراض تجميلية أو للراحة فقط.
- أن يجعل الأنواع الخارجية أكثر صعوبة في البناء أو الاستخدام.
- أن يؤثر على القيم الصفرية للأنواع الخارجية. إذا كان للنوع الخارجي قيمة صفرية مفيدة، فيجب أن يظل له قيمة صفرية مفيدة بعد تضمين النوع الداخلي.
- أن يكشف عن وظائف أو حقول غير ذات صلة من النوع الخارجي كتأثير جانبي لتضمين النوع الداخلي.
- أن يكشف عن أنواع غير مصدرة.
- أن يؤثر على دلالات النسخ للأنواع الخارجية.
- أن يغير واجهة برمجة التطبيقات للنوع الخارجي أو دلالات النوع.
- أن يضمن شكلًا غير قانوني للنوع الداخلي.
- أن يكشف عن تفاصيل التنفيذ للنوع الخارجي.
- أن يسمح للمستخدمين بمراقبة أو التحكم في داخليات النوع.
- أن يغير السلوك العام للدوال الداخلية من خلال التغليف بطريقة قد تفاجئ المستخدمين بشكل معقول.

ببساطة، قم بالتضمين بوعي وقصد. اختبار جيد هو "هل ستتم إضافة كل هذه الطرق/الحقول الداخلية المصدرة مباشرة إلى النوع الخارجي"؛ إذا كانت الإجابة "بعضها" أو "لا"، فلا تقم بتضمين النوع الداخلي - استخدم حقلًا بدلاً من ذلك.

<table>
<thead><tr><th>سيء</th><th>جيد</th></tr></thead>
<tbody>
<tr><td>

```go
type A struct {
    // سيء: A.Lock() و A.Unlock() متاحان الآن،
    // ولا يقدمان فائدة وظيفية،
    // ويسمحان للمستخدمين بالتحكم في تفاصيل
    // عن داخليات A.
    sync.Mutex
}
```

</td><td>

```go
type countingWriteCloser struct {
    // جيد: Write() متوفرة في هذه
    // الطبقة الخارجية لغرض محدد،
    // وتفوض العمل إلى Write() للنوع الداخلي.
    io.WriteCloser

    count int
}

func (w *countingWriteCloser) Write(bs []byte) (int, error) {
    w.count += len(bs)
    return w.WriteCloser.Write(bs)
}
```

</td></tr>
<tr><td>

```go
type Book struct {
    // سيء: المؤشر يغير فائدة القيمة الصفرية
    io.ReadWriter

    // حقول أخرى
}

// لاحقًا

var b Book
b.Read(...)  // panic: مؤشر فارغ
b.String()   // panic: مؤشر فارغ
b.Write(...) // panic: مؤشر فارغ
```

</td><td>

```go
type Book struct {
    // جيد: لها قيمة صفرية مفيدة
    bytes.Buffer

    // حقول أخرى
}

// لاحقًا

var b Book
b.Read(...)  // موافق
b.String()   // موافق
b.Write(...) // موافق
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

### إعلانات المتغيرات المحلية

يجب استخدام إعلانات المتغيرات القصيرة (`:=`) إذا كان يتم تعيين قيمة للمتغير بشكل صريح.

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

ومع ذلك، هناك حالات تكون فيها القيمة الافتراضية أوضح عند استخدام الكلمة المفتاحية `var`. [الإعلان عن شرائح فارغة](https://go.dev/wiki/CodeReviewComments#declaring-empty-slices)، على سبيل المثال.

<table>
<thead><tr><th>سيء</th><th>جيد</th></tr></thead>
<tbody>
<tr><td>

```go
func f(list []int) {
  filtered := []int{}
  for _, v := range list {
    if v > 10 {
      filtered = append(filtered, v)
    }
  }
}
```

</td><td>

```go
func f(list []int) {
  var filtered []int
  for _, v := range list {
    if v > 10 {
      filtered = append(filtered, v)
    }
  }
}
```

</td></tr>
</tbody></table>

### nil هو شريحة صالحة

`nil` هو شريحة صالحة ذات طول 0. هذا يعني أنه،

- لا ينبغي إعادة شريحة ذات طول صفر بشكل صريح. أعد `nil` بدلاً من ذلك.

  <table>
  <thead><tr><th>سيء</th><th>جيد</th></tr></thead>
  <tbody>
  <tr><td>

  ```go
  if x == "" {
    return []int{}
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

- للتحقق مما إذا كانت الشريحة فارغة، استخدم دائمًا `len(s) == 0`. لا تتحقق من `nil`.

  <table>
  <thead><tr><th>سيء</th><th>جيد</th></tr></thead>
  <tbody>
  <tr><td>

  ```go
  func isEmpty(s []string) bool {
    return s == nil
  }
  ```

  </td><td>

  ```go
  func isEmpty(s []string) bool {
    return len(s) == 0
  }
  ```

  </td></tr>
  </tbody></table>

- القيمة الصفرية (شريحة معلنة بـ `var`) قابلة للاستخدام على الفور بدون `make()`.

  <table>
  <thead><tr><th>سيء</th><th>جيد</th></tr></thead>
  <tbody>
  <tr><td>

  ```go
  nums := []int{}
  // أو، nums := make([]int)

  if add1 {
    nums = append(nums, 1)
  }

  if add2 {
    nums = append(nums, 2)
  }
  ```

  </td><td>

  ```go
  var nums []int

  if add1 {
    nums = append(nums, 1)
  }

  if add2 {
    nums = append(nums, 2)
  }
  ```

  </td></tr>
  </tbody></table>

تذكر أنه، على الرغم من أنها شريحة صالحة، فإن شريحة nil ليست مكافئة لشريحة مخصصة ذات طول 0 - واحدة هي nil والأخرى ليست كذلك - ويمكن معاملة الاثنتين بشكل مختلف في مواقف مختلفة (مثل التسلسل).

### تقليل نطاق المتغيرات

حيثما أمكن، قلل نطاق المتغيرات والثوابت. لا تقلل النطاق إذا كان يتعارض مع [تقليل التداخل](#reduce-nesting).

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

إذا كنت بحاجة إلى نتيجة دالة خارج جملة if، فلا يجب أن تحاول تقليل النطاق.

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

لا تحتاج الثوابت إلى أن تكون عالمية إلا إذا كانت تستخدم في وظائف أو ملفات متعددة أو جزءًا من عقد خارجي للحزمة.

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

### تجنب المعلمات العارية

يمكن أن تضر المعلمات العارية في استدعاءات الدوال بإمكانية القراءة. أضف تعليقات بأسلوب C (`/* ... */`) لأسماء المعلمات عندما لا يكون معناها واضحًا.

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

الأفضل من ذلك، استبدل أنواع `bool` العارية بأنواع مخصصة للحصول على كود أكثر قابلية للقراءة وأمانًا من حيث النوع. هذا يسمح بأكثر من حالتين فقط (صحيح/خطأ) لهذه المعلمة في المستقبل.

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

### استخدم الحرفيات النصية الخام لتجنب الهروب

يدعم Go [الحرفيات النصية الخام](https://go.dev/ref/spec#raw_string_lit)، التي يمكن أن تمتد على عدة أسطر وتتضمن علامات اقتباس. استخدم هذه لتجنب السلاسل المهروبة يدويًا والتي يصعب قراءتها بكثير.

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

### تهيئة الهياكل

#### استخدم أسماء الحقول لتهيئة الهياكل

يجب عليك دائمًا تقريبًا تحديد أسماء الحقول عند تهيئة الهياكل. هذا الآن مفروض بواسطة [`go vet`](https://pkg.go.dev/cmd/vet).

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

استثناء: *يمكن* حذف أسماء الحقول في جداول الاختبار عندما يكون هناك 3 حقول أو أقل.

```go
tests := []struct{
  op Operation
  want string
}{
  {Add, "add"},
  {Subtract, "subtract"},
}
```

#### حذف الحقول ذات القيمة الصفرية في الهياكل

عند تهيئة الهياكل بأسماء الحقول، احذف الحقول التي لها قيم صفرية إلا إذا كانت توفر سياقًا ذا معنى. خلاف ذلك، دع Go يعين هذه القيم الصفرية تلقائيًا.

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

هذا يساعد على تقليل الضوضاء للقراء من خلال حذف القيم التي تكون افتراضية في هذا السياق. يتم تحديد القيم ذات المعنى فقط.

تضمين القيم الصفرية حيث توفر أسماء الحقول سياقًا ذا معنى. على سبيل المثال، يمكن أن تستفيد حالات الاختبار في [جداول الاختبار](#test-tables) من أسماء الحقول حتى عندما تكون قيمها صفرية.

```go
tests := []struct{
  give string
  want int
}{
  {give: "0", want: 0},
  // ...
}
```

#### استخدم `var` للهياكل ذات القيمة الصفرية

عندما يتم حذف جميع حقول الهيكل في الإعلان، استخدم نموذج `var` للإعلان عن الهيكل.

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

هذا يميز الهياكل ذات القيم الصفرية عن تلك التي لها حقول غير صفرية مماثلة للتمييز الذي تم إنشاؤه لـ [تهيئة الخرائط](#initializing-maps)، ويتطابق مع كيفية تفضيلنا لـ [الإعلان عن شرائح فارغة](https://go.dev/wiki/CodeReviewComments#declaring-empty-slices).

#### تهيئة مراجع الهياكل

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

### تهيئة الخرائط

فضّل استخدام `make(..)` للخرائط الفارغة، والخرائط المملوءة برمجياً. هذا يجعل تهيئة الخريطة مختلفة بصرياً عن الإعلان، ويسهل إضافة تلميحات الحجم لاحقاً إذا كانت متاحة.

<table>
<thead><tr><th>سيء</th><th>جيد</th></tr></thead>
<tbody>
<tr><td>

```go
var (
  // m1 آمن للقراءة والكتابة؛
  // m2 سيتسبب في حدوث ذعر (panic) عند الكتابة.
  m1 = map[T1]T2{}
  m2 map[T1]T2
)
```

</td><td>

```go
var (
  // m1 آمن للقراءة والكتابة؛
  // m2 سيتسبب في حدوث ذعر (panic) عند الكتابة.
  m1 = make(map[T1]T2)
  m2 map[T1]T2
)
```

</td></tr>
<tr><td>

الإعلان والتهيئة متشابهان بصرياً.

</td><td>

الإعلان والتهيئة مختلفان بصرياً.

</td></tr>
</tbody></table>

حيثما أمكن، قدم تلميحات السعة عند تهيئة الخرائط باستخدام `make()`. انظر
[تحديد تلميحات سعة الخريطة](#specifying-map-capacity-hints)
لمزيد من المعلومات.

من ناحية أخرى، إذا كانت الخريطة تحتفظ بقائمة ثابتة من العناصر، استخدم حرفيات الخريطة لتهيئة الخريطة.

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

القاعدة الأساسية هي استخدام حرفيات الخريطة عند إضافة مجموعة ثابتة من العناصر في وقت التهيئة، وإلا استخدم `make` (وحدد تلميح الحجم إذا كان متاحاً).

### سلاسل التنسيق خارج Printf

إذا أعلنت عن سلاسل تنسيق لدوال بأسلوب `Printf` خارج حرف نصي، اجعلها قيم `const`.

هذا يساعد `go vet` على إجراء تحليل ثابت لسلسلة التنسيق.

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

### تسمية الدوال بأسلوب Printf

عندما تعلن عن دالة بأسلوب `Printf`، تأكد من أن `go vet` يمكنه اكتشافها والتحقق من سلسلة التنسيق.

هذا يعني أنه يجب عليك استخدام أسماء دوال `Printf` المحددة مسبقاً إذا أمكن. سيتحقق `go vet` من هذه افتراضياً. انظر [عائلة Printf](https://pkg.go.dev/cmd/vet#hdr-Printf_family)
لمزيد من المعلومات.

إذا لم يكن استخدام الأسماء المحددة مسبقاً خياراً، أنهِ الاسم الذي تختاره بـ f: `Wrapf`، وليس `Wrap`. يمكن أن يُطلب من `go vet` التحقق من أسماء أسلوب `Printf` محددة ولكن يجب أن تنتهي بـ f.

```shell
go vet -printfuncs=wrapf,statusf
```

انظر أيضاً [go vet: Printf family check](https://kuzminva.wordpress.com/2017/11/07/go-vet-printf-family-check/).

## الأنماط

### جداول الاختبار

يمكن أن تكون الاختبارات المدفوعة بالجداول مع [الاختبارات الفرعية](https://go.dev/blog/subtests) نمطاً مفيداً لكتابة الاختبارات لتجنب تكرار الكود عندما يكون منطق الاختبار الأساسي متكرراً.

إذا كان النظام الذي يتم اختباره يحتاج إلى الاختبار مقابل *شروط متعددة* حيث تتغير أجزاء معينة من المدخلات والمخرجات، فيجب استخدام اختبار مدفوع بالجدول لتقليل التكرار وتحسين إمكانية القراءة.

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

tests := []struct{
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

جداول الاختبار تجعل من الأسهل إضافة سياق لرسائل الخطأ، وتقليل المنطق المكرر، وإضافة حالات اختبار جديدة.

نتبع الاتفاقية التي تشير إلى أن شريحة الهياكل تسمى `tests` وكل حالة اختبار `tt`. بالإضافة إلى ذلك، نشجع على توضيح قيم الإدخال والإخراج لكل حالة اختبار مع بادئات `give` و `want`.

```go
tests := []struct{
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

#### تجنب التعقيد غير الضروري في اختبارات الجداول

يمكن أن تكون اختبارات الجداول صعبة القراءة والصيانة إذا كانت الاختبارات الفرعية تحتوي على تأكيدات شرطية أو منطق تفريع آخر. يجب **عدم** استخدام اختبارات الجداول كلما كانت هناك حاجة إلى منطق معقد أو شرطي داخل الاختبارات الفرعية (أي منطق معقد داخل حلقة `for`).

تضر اختبارات الجداول الكبيرة والمعقدة بإمكانية القراءة والصيانة لأن قراء الاختبار قد يواجهون صعوبة في تصحيح إخفاقات الاختبار التي تحدث.

يجب تقسيم اختبارات الجداول مثل هذه إما إلى جداول اختبار متعددة أو وظائف `Test...` فردية متعددة.

بعض المثل التي يجب السعي لتحقيقها هي:

* التركيز على أضيق وحدة من السلوك
* تقليل "عمق الاختبار"، وتجنب التأكيدات الشرطية (انظر أدناه)
* التأكد من استخدام جميع حقول الجدول في جميع الاختبارات
* التأكد من تشغيل جميع منطق الاختبار لجميع حالات الجدول

في هذا السياق، "عمق الاختبار" يعني "ضمن اختبار معين، عدد التأكيدات المتتالية التي تتطلب أن تصمد التأكيدات السابقة" (مماثل
للتعقيد السيكلوماتي).
وجود اختبارات "أقل عمقاً" يعني أن هناك علاقات أقل بين التأكيدات والأهم من ذلك، أن هذه التأكيدات أقل احتمالاً أن تكون شرطية افتراضياً.

بشكل ملموس، يمكن أن تصبح اختبارات الجداول مربكة وصعبة القراءة إذا كانت تستخدم مسارات تفرع متعددة
(مثل `shouldError` و `expectCall` وغيرها)، أو تستخدم العديد من عبارات `if` لتوقعات وهمية محددة (مثل `shouldCallFoo`)، أو تضع الدوال داخل الجدول (مثل `setupMocks func(*FooMock)`).

ومع ذلك، عند اختبار السلوك الذي يتغير فقط
بناءً على تغيير الإدخال، قد يكون من الأفضل تجميع الحالات المتشابهة
معًا في اختبار جدول لتوضيح كيفية تغير السلوك عبر جميع المدخلات بشكل أفضل،
بدلاً من تقسيم الوحدات القابلة للمقارنة إلى اختبارات منفصلة
وجعلها أكثر صعوبة في المقارنة والتباين.

إذا كان نص الاختبار قصيرًا وبسيطًا،
فمن المقبول أن يكون هناك مسار تفرع واحد لحالات النجاح مقابل الفشل
مع حقل جدول مثل `shouldErr` لتحديد توقعات الخطأ.

<table>
<thead><tr><th>سيء</th><th>جيد</th></tr></thead>
<tbody>
<tr><td>

```go
func TestComplicatedTable(t *testing.T) {
  tests := []struct {
    give          string
    want          string
    wantErr       error
    shouldCallX   bool
    shouldCallY   bool
    giveXResponse string
    giveXErr      error
    giveYResponse string
    giveYErr      error
  }{
    // ...
  }

  for _, tt := range tests {
    t.Run(tt.give, func(t *testing.T) {
      // إعداد وهمي
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

      // التحقق من النتائج
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
  // إعداد وهمي
  ctrl := gomock.NewController(t)
  xMock := xmock.NewMockX(ctrl)
  xMock.EXPECT().Call().Return("XResponse", nil)

  yMock := ymock.NewMockY(ctrl)

  got, err := DoComplexThing("inputX", xMock, yMock)

  require.NoError(t, err)
  assert.Equal(t, "want", got)
}

func TestShouldCallYAndFail(t *testing.T) {
  // إعداد وهمي
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

هذا التعقيد يجعل من الصعب تغيير الاختبار وفهمه وإثبات صحته.

على الرغم من عدم وجود إرشادات صارمة، يجب أن تكون إمكانية القراءة والصيانة دائمًا في مقدمة الاعتبارات عند الاختيار بين اختبارات الجداول مقابل الاختبارات المنفصلة للمدخلات/المخرجات المتعددة لنظام ما.

#### الاختبارات المتوازية

الاختبارات المتوازية، مثل بعض الحلقات المتخصصة (على سبيل المثال، تلك التي تنشئ goroutines أو تلتقط المراجع كجزء من جسم الحلقة)، يجب أن تأخذ الحذر لتعيين متغيرات الحلقة بشكل صريح داخل نطاق الحلقة لضمان أنها تحتفظ بالقيم المتوقعة.

```go
tests := []struct{
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

في المثال أعلاه، يجب علينا إعلان متغير `tt` محدد النطاق لتكرار الحلقة بسبب استخدام `t.Parallel()` أدناه.
إذا لم نفعل ذلك، فإن معظم أو كل الاختبارات ستتلقى قيمة غير متوقعة لـ `tt`، أو قيمة تتغير أثناء تشغيلها.

<!-- TODO: Explain how to use _test packages. -->

### خيارات وظيفية

الخيارات الوظيفية هي نمط تعلن فيه عن نوع `Option` غير شفاف يسجل معلومات في بعض الهياكل الداخلية. أنت تقبل عددًا متغيرًا من هذه الخيارات وتعمل على المعلومات الكاملة المسجلة بواسطة الخيارات على الهيكل الداخلي.

استخدم هذا النمط للوسائط الاختيارية في المنشئات وواجهات برمجة التطبيقات العامة الأخرى التي تتوقع الحاجة إلى توسيعها، خاصة إذا كان لديك بالفعل ثلاث وسائط أو أكثر في تلك الدوال.

<table>
<thead><tr><th>سيء</th><th>جيد</th></tr></thead>
<tbody>
<tr><td>

```go
// حزمة db

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
// حزمة db

type Option interface {
  // ...
}

func WithCache(c bool) Option {
  // ...
}

func WithLogger(log *zap.Logger) Option {
  // ...
}

// Open يُنشئ اتصالاً
func Open(
  addr string,
  opts ...Option,
) (*Connection, error) {
  // ...
}
```

</td></tr>
<tr><td>

يجب دائمًا توفير معلمات cache و logger، حتى إذا كان المستخدم
يريد استخدام الإعداد الافتراضي.

```go
db.Open(addr, db.DefaultCache, zap.NewNop())
db.Open(addr, db.DefaultCache, log)
db.Open(addr, false /* cache */, zap.NewNop())
db.Open(addr, false /* cache */, log)
```

</td><td>

يتم توفير الخيارات فقط عند الحاجة.

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

طريقتنا المقترحة لتنفيذ هذا النمط هي باستخدام واجهة `Option`
التي تحتوي على طريقة غير مصدرة، تسجل الخيارات على هيكل `options` غير مصدر.

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

// Open يُنشئ اتصالاً
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

لاحظ أن هناك طريقة لتنفيذ هذا النمط باستخدام الإغلاقات (closures) ولكننا
نعتقد أن النمط أعلاه يوفر مرونة أكبر للمؤلفين وهو
أسهل في التصحيح والاختبار للمستخدمين. على وجه الخصوص، يتيح مقارنة الخيارات
مع بعضها البعض في الاختبارات والمحاكاة، مقابل الإغلاقات حيث هذا
مستحيل. علاوة على ذلك، يتيح للخيارات تنفيذ واجهات أخرى، بما في ذلك
`fmt.Stringer` مما يسمح بتمثيل الخيارات المقروءة للمستخدم.

انظر أيضًا،

- [Self-referential functions and the design of options](https://commandcenter.blogspot.com/2014/01/self-referential-functions-and-design.html)
- [Functional options for friendly APIs](https://dave.cheney.net/2014/10/17/functional-options-for-friendly-apis)

<!-- TODO: replace this with parameter structs and functional options, when to
use one vs other -->

## التدقيق اللغوي (Linting)

الأهم من أي مجموعة "مباركة" من المدققات اللغوية، هو التدقيق اللغوي بشكل متسق عبر قاعدة الكود.

نوصي باستخدام المدققات اللغوية التالية كحد أدنى، لأننا نشعر أنها
تساعد في التقاط المشكلات الأكثر شيوعًا وأيضًا إنشاء مستوى عالٍ لجودة الكود
دون أن تكون وصفية بشكل غير ضروري:

- [errcheck](https://github.com/kisielk/errcheck) للتأكد من معالجة الأخطاء
- [goimports](https://pkg.go.dev/golang.org/x/tools/cmd/goimports) لتنسيق الكود وإدارة الاستيرادات
- [golint](https://github.com/golang/lint) للإشارة إلى أخطاء النمط الشائعة
- [govet](https://pkg.go.dev/cmd/vet) لتحليل الكود للأخطاء الشائعة
- [staticcheck](https://staticcheck.dev) للقيام بعمليات تحقق تحليل ثابت متنوعة

### مشغلات التدقيق اللغوي

نوصي بـ [golangci-lint](https://github.com/golangci/golangci-lint) كمشغل للتدقيق اللغوي لكود Go، إلى حد كبير بسبب
أدائه في قواعد الكود الأكبر والقدرة على تكوين واستخدام العديد
من المدققات اللغوية القانونية في وقت واحد. يحتوي هذا المستودع على مثال [.golangci.yml](https://github.com/uber-go/guide/blob/master/.golangci.yml) لملف التكوين
مع المدققات اللغوية الموصى بها والإعدادات.

golangci-lint لديه [مدققات لغوية متنوعة](https://golangci-lint.run/usage/linters/) متاحة للاستخدام. المدققات اللغوية المذكورة أعلاه هي
موصى بها كمجموعة أساسية، ونشجع الفرق على إضافة أي مدققات لغوية إضافية
تكون منطقية لمشاريعهم.