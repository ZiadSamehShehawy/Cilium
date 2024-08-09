# Cilium Network Policy
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
هنا الشرح:

- **metadata:**
  - **name**: اسم السياسة، في هذه الحالة هو `restrict-deathstar-access`.
  - **namespace**: الـ namespace الذي ستنطبق عليه السياسة، وهو `default`.

- **spec:**
  - **podSelector**: يحدد البودات المستهدفة بالسياسة:
    - **matchLabels**: يحدد التصنيفات التي يجب أن تكون موجودة في البودات. في هذه الحالة، `org: empire` و `class: deathstar`.
  - **policyTypes**: يحدد نوع السياسة، وهنا هي `Ingress` فقط (دخول).
  - **ingress**: يحدد القواعد التي ستنطبق على الـ ingress:
    - **from**: يحدد من أين سيتم السماح بالدخول:
      - **podSelector**:
        - **matchLabels**: يحدد التصنيفات التي يجب أن تكون موجودة في البودات التي سيتم السماح لها بالدخول، وهنا `org: empire`.
    - **ports**: يحدد البورتات والبروتوكولات التي سيتم السماح بالدخول لها:
      - **protocol**: `TCP`، وهو البروتوكول.
      - **port**: `80`، وهو رقم البورت.
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


# **Cilium LoadBalancer IPAM and L2 Service Announcement**

---

### مقدمة:

بما إننا مش بنستخدم مزود خدمة سحابية بيدعم الـ Load Balancer، هنستفيد من قدرات **Cilium** اللي بتدير عناوين IP وتعلن عنها على مستوى Layer 2 في الشبكة. عشان نفهم أكتر، هنطبق مع بعض خطوات **Cilium LoadBalancer IPAM and L2 Service Announcement**.

---

3. **تثبيت Cilium:**
   - هنستخدم أداة **Cilium CLI** ونضيف بعض الخيارات لرمز **Helm** باستخدام العلامة `--set`.

#### تكوين Helm:

- هنا القيم اللي بنستخدمها في **Helm** عشان نكون إعلانات Layer 2 (L2):

  ```yaml
  kubeProxyReplacement: strict
  l2announcements:
    enabled: true
  devices: {eth0, net0}
  externalIPs:
    enabled: true
  ```

#### خطوات التثبيت:

1. **تثبيت Cilium:**

   ```bash
   cilium install \
     --version v1.16.0 \
     --set kubeProxyReplacement=true \
     --set k8sServiceHost="kind-control-plane" \
     --set k8sServicePort=6443 \
     --set l2announcements.enabled=true \
     --set l2announcements.leaseDuration="3s" \
     --set l2announcements.leaseRenewDeadline="1s" \
     --set l2announcements.leaseRetryPeriod="500ms" \
     --set devices="{eth0,net0}" \
     --set externalIPs.enabled=true \
     --set operator.replicas=2
   ```

2. **تمكين Hubble للتصور:**

   ```bash
   cilium hubble enable --ui
   ```

3. **التحقق من تشغيل Cilium:**

   ```bash
   cilium status --wait
   ```

4. **التحقق من إعدادات إعلانات L2:**

   ```bash
   cilium config view | grep l2
   ```

---

### Hubble:

**Hubble** هو أداة بتستخدم لمراقبة وتحليل حركة البيانات في Kubernetes Cluster باستخدام **Cilium**. بتديك رؤية واضحة عن حركة البيانات بين التطبيقات والخدمات اللي شغالة جوه Cluster.

#### أهمية Hubble:

- **مراقبة حركة البيانات:** بتساعدك تتابع حركة البيانات اللي بتحصل بين التطبيقات والخدمات المختلفة.
- **تتبع الاتصال:** بتعرف مين بيتواصل مع مين وأوقات الاستجابة.
- **التحليل الأمني:** بتكشف الأنشطة اللي ممكن تكون مش مصرح بيها أو غريبة في الشبكة.
- **التصور المرئي:** بتوريك بشكل مرئي العلاقات بين مكونات الـ Kubernetes.

#### تمكين Hubble:

لو عايز تشغل **Hubble** في Cluster بتاعك، هتستخدم الأمر ده:

```bash
cilium hubble enable --ui
```

الأمر ده بيعمل الآتي:
- بيثبت **Hubble** في Kubernetes Cluster.
- بيشغل واجهة المستخدم الرسومية (UI) عشان تقدر تشوف البيانات بشكل مرئي.

#### استخدامات Hubble:

- **رؤية تفصيلية لحركة البيانات:** بتقدر تشوف الرسائل (packets) اللي بتتنقل بين التطبيقات والخدمات.
- **تحليل الأداء:** بتعرف إيه اللي بيبطئ الاستجابة أو بيعمل مشاكل في الاتصال.
- **التحقق من السياسات الأمنية:** بتشوف إذا كانت السياسات الأمنية شغالة تمام ولا لأ.
- **استكشاف الأخطاء:** بتحدد مصدر المشاكل في الشبكة وأسبابها.

---

### تمكين L2 IP Announcements:

الـ **L2 IP Announcements** في **Cilium** هو طريقة للإعلان عن عناوين IP للخدمات على مستوى Layer 2 في الشبكة. بعد ما نزل الإصدار **Cilium 1.13**، بقى في ميزة جديدة تسهل إنشاء خدمات **Load Balancer** والإعلان عنها باستخدام بروتوكول **BGP**.

#### شرح L2 IP Announcements:

1. **إيه هو Layer 2 (L2)؟**
   - ال Layer 2 هو المستوى اللي بيتعامل مع عناوين **MAC** اللي بتستخدم في نقل البيانات بين الأجهزة على نفس الشبكة المحلية (LAN).
   - الـ L2 بيستخدم **ARP** (Address Resolution Protocol) اللي بيخلي الأجهزة تقدر تعرف عنوان MAC اللي مرتبط بعنوان IP معين.

2. **إيه هو BGP؟**
   - ال **BGP** (Border Gateway Protocol) هو بروتوكول بيستخدم في الشبكات الكبيرة عشان يبادل معلومات التوجيه بين الـ Routers في الشبكات الواسعة (WAN).
   - ال BGP بيعلن عن العناوين عبر الشبكة الكبيرة لكنه محتاج بنية تحتية تدعمه.

3. **ليه نحتاج لإعلانات L2 IP؟**
   - في حالة إن Kubernetes Cluster بتاعك شغال على خوادم محلية (on-premise) ومش في بيئة سحابية، الشبكة ممكن ما تكونش بتدعم BGP.
   - هنا **Cilium** بتقدم لك خيار بديل باستخدام **ARP** في Layer 2 عشان توصل الإعلانات للأجهزة اللي على نفس الشبكة المحلية.

4. **إزاي بيشتغل ARP هنا؟**
   - ال **ARP** بيخلي الأجهزة على شبكة LAN تقدر تعرف عنوان MAC المرتبط بعنوان IP معين.
   - بفضل **ARP**، **Cilium** بتقدر تعلن عن عناوين IP الخاصة بالخدمات اللي تم إنشاؤها جوه Kubernetes للأجهزة على نفس الشبكة المحلية.

#### إعداد L2 IP Announcements في Cilium:

- عشان تشغل الميزة دي، لازم تفعّل إعدادات `l2announcements` في تكوين **Cilium**.
- ممكن تعمل ده من خلال إعدادات **Helm chart** عند تثبيت **Cilium** أو بتعديل إعدادات التكوين مباشرة.

### خطوات تمكين L2 IP Announcements:

عند تثبيت **Cilium**، هتستخدم الخيارات دي لتفعيل L2 Announcements:

```yaml
l2announcements:
  enabled: true
  devices: {eth0, net0}
```

- ال `enabled: true` معناها إن الإعلانات مفعّلة.
- ال `devices` بتحدد واجهات الشبكة اللي هيشتغل عليها **ARP**.

---

### تحديد Lease:

اسم الـ **Lease** في **Cilium** بيتكون من أجزاء بتوصف سياق الإعلان وبيتعلق بخدمة معينة وتفاصيل السياسة الخاصة بيها. خلينا نفهم إزاي الاسم بيتحدد:

#### مكونات اسم الـ Lease:

1. ال `cilium-l2announce`: ده الجزء اللي بيوضح نوع المورد. هنا بنقول إن المورد هو "L2 Announcement" من Cilium.
2. ال `default`: ده اسم الـ Namespace اللي الخدمة تبعته. هنا اسمه `default`.
3. ال `deathstar-2`: ده اسم الخدمة (أو التطبيق) اللي مرتبط بالـ Lease. هنا الخدمة اسمها `deathstar-2`.

#### إزاي بيتحدد اسم الـ Lease:

- بناءً على السياسة:
  - لما تعمل سياسة L2 Announcement، **Cilium** بيخلق Lease بالاسم اللي بيشمل جزء من اسم السياسة أو الخدمة اللي السياسة مطبقة عليها.
- موقع الـ Lease:
  - الـ Lease بيتخلق في الـ Namespace اللي فيه الخدمة أو التطبيق. هنا الـ Namespace اسمه `default`.
- تحديد الخدمة:
  - اسم الخدمة بيتضاف للـ Lease عشان يحدد الـ Lease اللي مرتبط بالخدمة دي. مثلاً لو الخدمة اسمها `deathstar-2`، جزء من الاسم هيكون `deathstar-2`.

#### مثال توضيحي:

لو عندك سياسة L2 Announcement مرتبطة بخدمة في Namespace اسمه `default` واسمها `deathstar-2`، اسم الـ Lease اللي هيخلقه Cilium هيكون:

`cilium-l2announce-default-deathstar-2`

#### خطوات عملية:

1. **تطبيق سياسة L2 Announcement:**
   - بتطبق السياسة اللي بتحدد إزاي الخدمة هتتعلن.
2. **التحقق من الـ Lease:**
   - بعد ما تطبق السياسة، ممكن تشوف الـ Lease باستخدام الأمر `kubectl get leases` وتلاحظ الاسم اللي اتخلق.

---

## **1. نشر خدمة Death Star**

### ملف YAML

أكيد، هننسق محتوى ملف `deathstar.yaml` بالشكل التالي:

```yaml
---
apiVersion: v1
kind: Service
metadata:
  name: deathstar
  labels:
    color: red
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
spec:
  selector:
    matchLabels:
      org: empire
      class: deathstar
  replicas: 2
  template:
    metadata:
      labels:
        org: empire
        class: deathstar
        name: deathstar
    spec:
      containers:
        - name: deathstar
          image: docker.io/cilium/starwars
          imagePullPolicy: IfNotPresent
```

### شرح الملف:

- **Service**: 
  - ال `kind: Service` يعنى انك بتمثل خدمة من نوع `ClusterIP` بتشتغل على المنفذ 80. الخدمة بتكون متاحة داخليًا داخل الكلاستر.
  - ال `selector` هنا بيتوافق مع `Deployment` اللى ليه نفس الـ labels عشان الخدمة تشتغل بشكل صحيح.

- **Deployment**:
  - ال `replicas: 2` يعنى إنه هيتم تشغيل نسختين من الـ Pod الخاص بتطبيق `deathstar`.
  - ال `containers` فيه تعريف الحاويات (Containers) الخاصة بالتطبيق، وتحديد صورة الحاوية اللى هتشتغل.

كل شيء مضبوط وجاهز للاستخدام في Kubernetes.


### Adding a Public IP to the Service

عاوز تضيف عنوان IP خارجي عشان تقدر تدخل على الخدمة من خارج الكلاستر؟ اتبع الخطوات دي:

```bash
SVC_IP=12.0.0.100
kubectl patch service deathstar -p '{"spec":{"externalIPs":["'$SVC_IP'"]}}'
```

### Verify Service with External IP

بعد كده، تأكد إن الخدمة بقت متاحة باستخدام الـ IP الخارجي:

```bash
kubectl get svc deathstar --show-labels
```

### Layer 2 Policy Configuration

لإعداد Layer 2 للإعلان عن الـ IP الخارجي للخدمة، استخدم الملف التالي:

```yaml
# l2policy.yaml
apiVersion: "cilium.io/v2alpha1"
kind: CiliumL2AnnouncementPolicy
metadata:
  name: policy1
spec:
  externalIPs: true
  loadBalancerIPs: false
  interfaces:
    - net0
  serviceSelector:
    matchLabels:
      color: red
  nodeSelector:
    matchExpressions:
      - key: node-role.kubernetes.io/control-plane
        operator: DoesNotExist
```

### Testing the Service from Inside the Pod

ادخل جوه الـ pod واعمل اختبار باستخدام `curl`:

```bash
docker exec -e SVC_IP=$SVC_IP -ti clab-garp-demo-neighbor bash
curl --connect-timeout 1 http://$SVC_IP/v1/
```

📌 **ملحوظة:** الخدمة دلوقتي بقت متاحة لأن الـ IP تم الإعلان عنه عبر ARP على الشبكة.

---

## **2. Visualize ARP Traffic**

### Step 1: عرض مورد Lease

ال Cilium بيعمل مورد Lease في namespace `kube-system` لكل L2 مرتبط بالخدمة. عشان تشوف تفاصيل Lease لخدمة `deathstar-2`، استخدم الأمر ده:

```bash
kubectl get leases -n kube-system cilium-l2announce-default-deathstar-2 -o yaml
```

### Step 2: استرجاع العقدة المستضيفة للـ Lease

الـ Lease بيحتوي على معلومات عن العقدة المستضيفة. عشان تحصل على اسم العقدة:

```bash
LEASE_NODE=$(kubectl -n kube-system get leases cilium-l2announce-default-deathstar-2 -o jsonpath='{.spec.holderIdentity}')
echo $LEASE_NODE
```

### Step 3: العثور على بود Cilium على العقدة

ابحث عن بود Cilium اللي شغال على العقدة المحددة:

```bash
LEASE_CILIUM_POD=$(kubectl -n kube-system get pod -l k8s-app=cilium --field-selector spec.nodeName=$LEASE_NODE -o name)
echo $LEASE_CILIUM_POD
```

### Step 4: تسجيل الدخول إلى بود Cilium

سجل الدخول لبود Cilium:

```bash
kubectl -n kube-system exec -ti $LEASE_CILIUM_POD -- bash
```

### Step 5: تثبيت tcpdump وtermshark في البود

بعد ما تدخل على البود، حدث النظام وثبت الأدوات اللازمة:

```bash
apt-get update && DEBIAN_FRONTEND=noninteractive apt-get -y install tcpdump termshark
```

### Step 6: تشغيل tcpdump لالتقاط حزم ARP

ابدأ تشغيل `tcpdump` في الخلفية:

```bash
tcpdump -i any arp -w arp.pcap
```

### الخطوات الإضافية

بعد تنفيذ الأوامر، تقدر تحمل ملف `arp.pcap` على جهازك المحلي وتستخدم أدوات زي Wireshark لتحليل الحزم.

---

## **3. شرح الخطوات**

### Step 1: إجراء طلب إلى الخدمة

في الخطوة دي، ابعت طلب للخدمة اللي تم تكوينها باستخدام عنوان IP الخارجي:

```bash
docker exec -ti clab-garp-demo-neighbor \
  curl --connect-timeout 1 http://12.0.0.101/v1/
```

### Step 2: إنهاء tcpdump

اضغط على Ctrl+C عشان توقف `tcpdump`.

### Step 3: إعداد Termshark

جهز `termshark` للعرض بالوضع الداكن:

```bash
mkdir -p /root/.config/termshark/
echo -e "[main]\ndark-mode = true" > /root/.config/termshark/termshark.toml
```

### Step 4: عرض حركة مرور ARP باستخدام Termshark

ابدأ تشغيل `termshark` لعرض حركة ARP:

```bash
TERM=xterm-256color termshark -r arp.pcap
```

---

## **4. Automatic IPAM**



في التحدي الأول، عملنا خدمة بعنوان IP ثابت في الـ manifest. 

دلوقتي، خلينا نشوف إزاي الـ IPs ممكن تتخصص تلقائياً للخدمات باستخدام Cilium.

---

### 1. إنشاء Pool جديد للعناوين

في البداية، هننشئ Pool جديد للعناوين ونستخدم `yq` عشان نعرض محتوى الملف:

```bash
root@server:~# yq pool-blue.yaml
```

#### محتويات ملف `pool-blue.yaml`:

```yaml
apiVersion: "cilium.io/v2alpha1"
kind: CiliumLoadBalancerIPPool
metadata:
  name: "pool-blue"
spec:
  blocks:
    - cidr: "12.0.0.128/25"
  serviceSelector:
    matchLabels:
      color: blue
```

### 2. إنشاء سياسة L2

بعد كده، هنعمل سياسة لإعلانات Layer 2 (L2) باستخدام الملف `l2policy.yaml`:

```bash
vim l2policy.yaml
```

#### محتويات ملف `l2policy.yaml`:

```yaml
apiVersion: "cilium.io/v2alpha1"
kind: CiliumL2AnnouncementPolicy
metadata:
  name: policy1
spec:
  externalIPs: true
  loadBalancerIPs: true  
  interfaces:
  - net0
  serviceSelector:
    matchLabels:
      color: blue
  nodeSelector:
    matchExpressions:
      - key: node-role.kubernetes.io/control-plane
        operator: DoesNotExist
```

### 3. إنشاء خدمة جديدة

دلوقتي، هنعمل خدمة جديدة باسم `deathstar-3` بدون تعيين عنوان IP ثابت:

```bash
kubectl expose deployment deathstar --name deathstar-3 --port 80 --type LoadBalancer
```

#### شرح الأوامر:
- ال `kubectl expose deployment deathstar`: يعرض تطبيق `deathstar` كخدمة.
- ال `--name deathstar-3`: يحدد اسم الخدمة كـ `deathstar-3`.
- ال `--port 80`: يحدد المنفذ اللي بتشتغل عليه الخدمة.
- ال `--type LoadBalancer`: يطلب نوع الخدمة ليكون `LoadBalancer`، اللي هيحاول يخصص عنوان IP خارجي.

### 4. التحقق من الخدمة

دلوقتي، هنتحقق من حالة الخدمة ونتأكد إنها ما حصلتش على عنوان IP خارجي لسه:

```bash
kubectl get svc deathstar-3 --show-labels
```

#### شرح الأوامر:
- `kubectl get svc deathstar-3`: يعرض معلومات الخدمة `deathstar-3`.

### 5. إضافة تسمية `color=blue`

هنضيف التسمية `color=blue` للخدمة:

```bash
kubectl label svc deathstar-3 color=blue
```

#### شرح الأوامر:
- ال `kubectl label svc deathstar-3 color=blue`: يضيف التسمية `color=blue` للخدمة.

### 6. التحقق من الخدمة مرة أخرى

هنتأكد مرة تانية إن الخدمة حصلت على عنوان IP خارجي:

```bash
kubectl get svc deathstar-3 --show-labels
```

#### شرح الأوامر:
- ال `kubectl get svc deathstar-3`: يعرض معلومات الخدمة مع التسمية المضافة.

### 7. التحقق من الوصول إلى الخدمة عبر ARP

هنحصل على عنوان IP الخارجي للخدمة ونتأكد من الوصول ليها:

```bash
SVC2_IP=$(kubectl get svc deathstar-3 -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
echo $SVC2_IP
docker exec -ti clab-garp-demo-neighbor curl --connect-timeout 1 $SVC2_IP/v1/
```

#### شرح الأوامر:
- ال `kubectl get svc deathstar-3 -o jsonpath='{.status.loadBalancer.ingress[0].ip}'`: بيحصل على عنوان IP الخارجي للخدمة.
- ال `docker exec -ti clab-garp-demo-neighbor curl --connect-timeout 1 $SVC2_IP/v1/`: بينفذ طلب HTTP للخدمة باستخدام عنوان IP الخارجي.

---


## **5. Removing a Node**

### الخطوات:

1. **تحديد العقدة الحالية:**

   استخدم مورد Lease لتحديد العقدة اللي بتحمل الـ IP الخاص بالخدمة.

   ```bash
   kubectl -n kube-system get leases cilium-l2announce-default-deathstar -o yaml | yq .spec
   ```

2. **استرجاع عنوان MAC:**

   احصل على عنوان MAC المرتبط بالـ IP.

   ```bash
   docker exec -ti clab-garp-demo-neighbor arp 12.0.0.100
   ```

3. **تحديد رقم veth pair:**

   ابحث عن رقم veth pair على العقدة باستخدام عنوان MAC اللي حصلت عليه.

   ```bash
   docker exec kind-worker2 ip a | grep -B1 aa:c1:ab:12:ca:75
   ```

4. **استرجاع اسم الواجهة على VM:**

   ابحث عن اسم الواجهة المرتبطة بـ veth pair على VM.

   ```bash
   ip a | grep if18
   ```

5. **محاكاة مشكلة على العقدة:**

   قم بإيقاف الحاوية اللي بتستضيف العقدة لمحاكاة فشل العقدة:

   ```bash
   docker kill kind-worker2
   ```

   واوقف واجهة veth المرتبطة بالعقدة:

   ```bash
   ip link set net3 down
   ```

6. **التحقق من تغيير holderIdentity:**

   تحقق من مورد Lease مرة أخرى لتأكيد إن `holderIdentity` اتغير لعقدة تانية.

   ```bash
   kubectl -n kube-system get leases cilium-l2announce-default-deathstar -o yaml | yq .spec.holderIdentity
   ```

---

## **HOL (Hands-On Lab)**

### التحدي العملي:

في هذا التحدي، هنقوم بعمل الآتي:

1. عرض Death Star على المنفذ 80 بخدمة جديدة من نوع LoadBalancer.
2. إنشاء Pool جديد للـ IPs باستخدام `Cilium Load-Balancer IP Pool`.
3. إنشاء سياسة إعلان جديدة لـ L2 للإعلان عن IPs الخدمة على الواجهة `eth0`.

### الحل:

أكيد، هظبط الأوامر كالتالي:

---

### 1. إنشاء Pool جديد للعناوين

في البداية، هننشئ Pool جديد للعناوين ونستخدم `yq` عشان نعرض محتوى الملف:

```bash
yq eval . pool-blue.yaml
```

#### محتويات ملف `pool-blue.yaml`:

```yaml
apiVersion: "cilium.io/v2alpha1"
kind: CiliumLoadBalancerIPPool
metadata:
  name: "pool-blue"
spec:
  blocks:
    - cidr: "12.0.0.128/25"
  serviceSelector:
    matchLabels:
      color: blue
```

---

### 2. إنشاء سياسة L2

بعد كده، هنعمل سياسة لإعلانات Layer 2 (L2) باستخدام الملف `l2policy.yaml`:

```bash
vim l2policy.yaml
```

#### محتويات ملف `l2policy.yaml`:

```yaml
apiVersion: "cilium.io/v2alpha1"
kind: CiliumL2AnnouncementPolicy
metadata:
  name: policy1
spec:
  externalIPs: true
  loadBalancerIPs: true  
  interfaces:
  - net0
  serviceSelector:
    matchLabels:
      color: blue
  nodeSelector:
    matchExpressions:
      - key: node-role.kubernetes.io/control-plane
        operator: DoesNotExist
```

---

### 3. إنشاء خدمة جديدة

دلوقتي، هنعمل خدمة جديدة باسم `deathstar-3` بدون تعيين عنوان IP ثابت:

```bash
kubectl expose deployment deathstar --name=deathstar-3 --port=80 --type=LoadBalancer
```

---

### 4. التحقق من الخدمة

دلوقتي، هنتحقق من حالة الخدمة ونتأكد إنها ما حصلتش على عنوان IP خارجي لسه:

```bash
kubectl get svc deathstar-3 --show-labels
```

---

### 5. إضافة تسمية `color=blue`

هنضيف التسمية `color=blue` للخدمة:

```bash
kubectl label svc deathstar-3 color=blue
```

---

### 6. التحقق من الخدمة مرة أخرى

هنتأكد مرة تانية إن الخدمة حصلت على عنوان IP خارجي:

```bash
kubectl get svc deathstar-3 --show-labels
```

---

### 7. التحقق من الوصول إلى الخدمة عبر ARP

هنحصل على عنوان IP الخارجي للخدمة ونتأكد من الوصول ليها:

```bash
SVC2_IP=$(kubectl get svc deathstar-3 -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
echo $SVC2_IP
docker exec -ti clab-garp-demo-neighbor curl --connect-timeout 1 $SVC2_IP/v1/
```
