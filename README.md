# Cilium
### Cilium and Endpoints

#### يعني إيه Endpoint في Cilium؟

في Cilium، كل بود (Pod) بيتعامل كـ Endpoint. الـ Endpoint ده بيمثل نقطة في الشبكة اللي ممكن تتواصل مع نقاط تانية (يعني بودات تانية).

#### إزاي نجيب قائمة بكل الـ Endpoints اللي بتديرها Cilium؟

عشان تجيب قائمة بكل الـ Endpoints اللي Cilium بيديرها، هنستخدم مورد اسمه Cilium Endpoint أو cep.

#### الأمر المطلوب (shell):

عشان نشوف القائمة دي، هتستخدم الأمر ده في الـ shell:

```sh
kubectl get cep --all-namespaces
```

#### شرح الأمر:

- `kubectl`: ده هو الأمر الأساسي اللي بنستخدمه لإدارة الـ Kubernetes cluster.
- `get`: ده يعني إنك عايز تجيب بيانات.
- `cep`: ده اختصار لـ Cilium Endpoint، اللي هو نوع المورد اللي إحنا بندور عليه.
- `--all-namespaces`: ده يعني إنك عايز تجيب القائمة من كل الـ namespaces في الكلاستر.

لما تشغل الأمر ده، هتظهر لك قائمة بكل الـ Endpoints اللي Cilium بيديرها في الكلاستر بتاعك. هتشوف تفاصيل زي أسماء الـ Endpoints، فين موجودين، وحالتهم الحالية.

### ليه الموضوع ده مهم؟

فهم الـ Endpoints وإزاي تجيب قائمتهم مهم عشان تقدر تراقب وتدير الشبكة الداخلية في الكلاستر بتاعك. ده بيساعدك في تحديد المشاكل لو فيه أي حاجة غلط، وكمان في تحسين أداء الشبكة.

### استخدام Cilium Endpoints

بعد ما فهمنا إزاي نجيب قائمة بالـ Endpoints اللي Cilium بيديرها، خلينا نشوف إيه اللي ممكن تعمله بالمعلومات دي.

#### متابعة وتحليل الشبكة

لما تجيب قائمة الـ Endpoints، هتقدر تستخدم المعلومات دي في عدة حاجات، زي:

1. **مراقبة الأداء**:
   - تقدر تشوف إذا كان فيه أي Endpoint مش شغال بشكل صحيح.
   - تقدر تراقب الـ traffic اللي بيعدي بين الـ Endpoints.

2. **تحليل المشاكل**:
   - لو فيه مشاكل في التواصل بين البودات، تقدر تستخدم القائمة دي لتحديد إذا كان فيه بود معين مش شغال.
   - تقدر تشوف الـ logs الخاصة بكل Endpoint لتحديد مصدر المشكلة.

#### أوامر إضافية للتفاعل مع الـ Endpoints

بمجرد ما عندك القائمة، تقدر تستخدم أوامر تانية للتفاعل مع الـ Endpoints دي. مثلاً:

1. **مشاهدة تفاصيل الـ Endpoint**:
   - لو عايز تشوف تفاصيل أكتر عن Endpoint معين، تقدر تستخدم الأمر ده:
     ```sh
     kubectl describe cep <endpoint-name> -n <namespace>
     ```
     استبدل `<endpoint-name>` باسم الـ Endpoint اللي عايز تشوف تفاصيله، و `<namespace>` باسم الـ namespace اللي هو فيه.

2. **حذف Endpoint**:
   - لو عايز تحذف Endpoint معين، تقدر تستخدم الأمر ده:
     ```sh
     kubectl delete cep <endpoint-name> -n <namespace>
     ```

#### مثال عملي

خلينا نقول إنك عايز تراقب Endpoint معين وتحلل أداءه:

1. احصل على قائمة الـ Endpoints:
   ```sh
   kubectl get cep --all-namespaces
   ```

2. شوف تفاصيل Endpoint معين:
   ```sh
   kubectl describe cep example-endpoint -n example-namespace
   ```

3. حلل الـ logs لو فيه مشكلة:
   - تقدر تشوف الـ logs الخاصة بالبود اللي Endpoint مرتبط بيه:
     ```sh
     kubectl logs example-pod -n example-namespace
     ```

### الاستفادة من الـ Cilium UI

انه ال Cilium بيقدم واجهة مستخدم (UI) ممكن تساعدك في تحليل الشبكة بشكل أفضل. تقدر تشغل الـ UI وتستخدمها لمتابعة وتحليل كل الـ Endpoints بطريقة أسهل.

#### تشغيل واجهة المستخدم لـ Cilium

1. شغل واجهة المستخدم:
   ```sh
   kubectl port-forward -n kube-system svc/cilium-metrics 9090:9090
   ```

2. افتح المتصفح:
   - افتح المتصفح على الرابط: [http://localhost:9090](http://localhost:9090)

في واجهة المستخدم، هتلاقي رسومات بيانية وتفاصيل عن أداء الشبكة والـ Endpoints اللي Cilium بيديرها.

### الخلاصة

استخدام Cilium Endpoints بيمكنك من مراقبة وتحليل أداء الشبكة في الـ Kubernetes cluster بتاعك بشكل دقيق. الأوامر والأدوات المتاحة بتساعدك تتابع وتحدد وتحل المشاكل بشكل فعال.

### اختبار الاتصال بين Microservices


```yaml
---
apiVersion: v1
kind: Service
metadata:
  name: deathstar
  labels:
    app.kubernetes.io/name: deathstar
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    org: empire
    class: deathstar
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: deathstar
  labels:
    app.kubernetes.io/name: deathstar
spec:
  replicas: 2
  selector:
    matchLabels:
      org: empire
      class: deathstar
  template:
    metadata:
      labels:
        org: empire
        class: deathstar
        app.kubernetes.io/name: deathstar
    spec:
      containers:
      - name: deathstar
        image: quay.io/cilium/starwars:v2.1@sha256:833d915ec68fca3ce83668fc5dae97c455b2134d8f23ef96586f55b894cfb1e8
---
apiVersion: v1
kind: Pod
metadata:
  name: tiefighter
  labels:
    org: empire
    class: tiefighter
    app.kubernetes.io/name: tiefighter
spec:
  containers:
  - name: spaceship
    image: quay.io/cilium/json-mock:v1.3.8@sha256:5aad04835eda9025fe4561ad31be77fd55309af8158ca8663a72f6abb78c2603
---
apiVersion: v1
kind: Pod
metadata:
  name: xwing
  labels:
    app.kubernetes.io/name: xwing
    org: alliance
    class: xwing
spec:
  containers:
  - name: spaceship
    image: quay.io/cilium/json-mock:v1.3.8@sha256:5aad04835eda9025fe4561ad31be77fd55309af8158ca8663a72f6abb78c2603
```

#### الأوامر لاختبار الاتصال:

1. **اختبار الاتصال بين `tiefighter` و `deathstar`**:
   ```sh
   kubectl exec tiefighter -- curl -s -XPOST deathstar.default.svc.cluster.local/v1/request-landing
   ```

2. **اختبار الاتصال بين `xwing` و `deathstar`**:
   ```sh
   kubectl exec xwing -- curl -s -XPOST deathstar.default.svc.cluster.local/v1/request-landing
   ```

### الحماية باستخدام Network Policy

#### تعريف الـ Network Policy:

في الحالة اللي عايز فيها تمنع الـ `xwing` من الوصول للـ `deathstar`، هنستخدم سياسة أمان تمنع الوصول الغير مصرح بيه.

#### كتابة الـ Network Policy:

ملف `policy.yml`:

```yaml
apiVersion: cilium.io/v2
kind: CiliumNetworkPolicy
metadata:
  name: rule1
spec:
  endpointSelector:
    matchLabels:
      org: empire
      class: deathstar
  ingress:
    - fromEndpoints:
        - matchLabels:
            org: empire
      toPorts:
        - ports:
            - port: "80"
              protocol: TCP
```

#### تطبيق الـ Network Policy:

```sh
kubectl apply -f policy.yml
```

#### اختبارال Policy:

1. **الاتصال من `tiefighter`**:
   ```sh
   kubectl exec tiefighter -- curl -s -XPOST deathstar.default.svc.cluster.local/v1/request-landing
   ```

2. **الاتصال من `xwing`**:
   ```sh
   kubectl exec xwing -- curl -s -XPOST deathstar.default.svc.cluster.local/v1/request-landing
   ```

### استخدام المسارات (Paths) في السياسة

لزيادة الأمان، ممكن تحدد المسارات المسموح بيها:

```yaml
apiVersion: cilium.io/v2
kind: CiliumNetworkPolicy
metadata:
  name: rule1
spec:
  endpointSelector:
    matchLabels:
      org: empire
      class: deathstar
  ingress:
    - fromEndpoints:
        - matchLabels:
            org: empire
      toPorts:
        - ports:
            - port: "80"
              protocol: TCP
          rules:
            http:
              - method: POST
                path: /v1/request-landing
```

### اختبار ال Paths:

1. **محاولة الوصول لمكان غير مسموح بيه**:
   ```sh
   kubectl exec tiefighter -- curl -s -XPUT deathstar.default.svc.cluster.local/v1/exhaust-port
   ```

2. **تطبيق ال Policy مع ال Paths**:
   ```sh
   kubectl apply -f policy.yml
   ```

3. **محاولة الوصول بعد تطبيق ال Policy**:
   ```sh
   kubectl exec tiefighter -- curl -s -XPUT deathstar.default.svc.cluster.local/v1/exhaust-port
   ```

### الخلاصة

استخدام Cilium Network Policies بيساعدك في حماية الـ Kubernetes cluster بتاعك عن طريق تحديد مين يقدر يوصل لمين وإيه العمليات اللي مسموح بيها. باستخدام الـ Policies دي، تقدر تحافظ على أمان الشبكة وتمنع أي وصول غير مصرح بيه.
