# Cilium Network Policy📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍
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

📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍
# **Cilium LoadBalancer IPAM and L2 Service Announcement**
📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍
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

----------------------------------------------------------------------------------------------------------------------
----------------------------------------------------------------------------------------------------------------------
#API Gateway
----------------------------------------------------------------------------------------------------------------------
----------------------------------------------------------------------------------------------------------------------

# 📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍
## 🚀 **API Gateway - كل اللي محتاج تعرفه!** 🚀
📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍

### 🎯 **يعني ايه Gateway API؟**
الـ **Gateway API** هو مجموعة من الموارد اللي في Kubernetes واللي بتخليك تتحكم في **حركة المرور** (traffic) اللي جاية من بره الـ **كلاستر** (cluster) ورايحة للخدمات الداخلية جوا الكلاستر. يعتبر الـ Gateway API هو التطور الطبيعي للـ **Ingress API**، وبيوفر ليك **تحكم أكتر** و**مرونة أكبر** في إدارة وتوجيه حركة المرور.

### 🔑 **المفاهيم الأساسية:**
- ال**Gateway**: نقطة دخول حركة المرور من بره الكلاستر.
- ال**GatewayClass**: تعريف لنوع الـ Gateway والمواصفات اللي بيدعمها.
- ال**HTTPRoute**: كيفية توجيه حركة المرور بناءً على المسار (path) أو الـ host.
- ال**TCPRoute**: كيفية توجيه حركة المرور بناءً على الـ TCP.
- ال**TLSRoute**: كيفية توجيه حركة المرور بناءً على الـ TLS.
- ال**BackendPolicy**: السياسات اللي بتتطبق على الخدمات الداخلية (backends).

### 💡 **الفايدة من Gateway API:**
1. **مرونة أكبر**: بيديك تحكم كامل في إدارة حركة المرور.
2. **قابلية التوسع**: بيسهل إدارة وتوجيه حركة المرور في كلاستر كبير ومعقد.
3. **تكامل أفضل**: بيسهل الدمج مع الأدوات والخدمات التانية.

### ⚙️ **مثال بسيط:**

لو عندك **خدمة ويب** عايز توصلها للناس من بره الكلاستر، تقدر تستخدم الـ Gateway API عشان تحدد بوابة (Gateway) وتوجه حركة المرور للخدمة بناءً على المسار (path) أو الـ host.

```yaml
apiVersion: gateway.networking.k8s.io/v1alpha2
kind: Gateway
metadata:
  name: my-gateway
spec:
  gatewayClassName: my-gateway-class
  listeners:
    - protocol: HTTP
      port: 80
---
apiVersion: gateway.networking.k8s.io/v1alpha2
kind: HTTPRoute
metadata:
  name: my-route
spec:
  parentRefs:
    - name: my-gateway
  rules:
    - matches:
        - path:
            type: Prefix
            value: /my-service
      forwardTo:
        - serviceName: my-service
          port: 80
```

📌 في المثال ده، الـ **Gateway** بيحدد نقطة دخول لحركة المرور على البورت 80، و`HTTPRoute` بيحدد إن حركة المرور اللي جاية على المسار `/my-service` تتوجه للخدمة `my-service` على البورت 80.

### 🔥 **الخلاصة:**
الـ **Gateway API** هو أداة قوية لإدارة وتوجيه حركة المرور في Kubernetes، بيساعدك تتحكم بشكل أفضل في حركة المرور اللي بتيجي من بره الكلاستر وبتروح للخدمات الداخلية.

---

## 📝 **شرح Gateway API:**
الـ **Gateway API** مشروع فرعي ضمن مشروع **Kubernetes SIG-Network** لاستبدال **Ingress**. الهدف منه هو توفير مجموعة من الموارد اللي بتمكن من نمذجة شبكة الخدمات داخل Kubernetes بشكل **توجيهي، قابل للنقل، وقابل للتوسيع**.

### 🛠️ **دعم Cilium لـ Gateway API:**
Cilium بيدعم Gateway API الإصدار v1.1.0 للموارد التالية:
- **GatewayClass**
- **Gateway**
- **HTTPRoute**
- **GRPCRoute**
- **TLSRoute** (تجريبي)
- **ReferenceGrant**

### 📋 **الخطوات المسبقة (Prerequisites):**
1. ال**NodePort**: لازم يكون Cilium مهيأ بتفعيل NodePort باستخدام `nodePort.enabled=true` أو بتفعيل استبدال kube-proxy باستخدام `kubeProxyReplacement=true`.
2. ال**L7 Proxy**: لازم يكون Cilium مهيأ بتفعيل L7 Proxy باستخدام `l7Proxy=true` (مفعل افتراضيًا).
3. ال**CRDs**: لازم تثبيت الموارد التالية من Gateway API v1.1.0 باستخدام الأوامر دي:

    ```sh
    kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/gateway-api/v1.1.0/config/crd/standard/gateway.networking.k8s.io_gatewayclasses.yaml
    kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/gateway-api/v1.1.0/config/crd/standard/gateway.networking.k8s.io_gateways.yaml
    kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/gateway-api/v1.1.0/config/crd/standard/gateway.networking.k8s.io_httproutes.yaml
    kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/gateway-api/v1.1.0/config/crd/standard/gateway.networking.k8s.io_referencegrants.yaml
    kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/gateway-api/v1.1.0/config/crd/standard/gateway.networking.k8s.io_grpcroutes.yaml
    kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/gateway-api/v1.1.0/config/crd/experimental/gateway.networking.k8s.io_tlsroutes.yaml
    ```

### ⚙️ **التركيب:**
1. التحقق من إصدار `cilium-cli`: تأكد من إن إصدار `cilium-cli` هو `v0.15.0` أو أحدث. للتحقق من الإصدار المثبت:

    ```sh
    cilium version --client
    ```


---

## تثبيت واستخدام Cilium مع Gateway API

### 1. نجيب أحدث إصدار من Cilium CLI



```bash
CILIUM_CLI_VERSION=$(curl -s https://raw.githubusercontent.com/cilium/cilium-cli/main/stable.txt)
CLI_ARCH=amd64
if [ "$(uname -m)" = "aarch64" ]; then CLI_ARCH=arm64; fi
curl -L --fail --remote-name-all https://github.com/cilium/cilium-cli/releases/download/${CILIUM_CLI_VERSION}/cilium-linux-${CLI_ARCH}.tar.gz{,.sha256sum}
sha256sum --check cilium-linux-${CLI_ARCH}.tar.gz.sha256sum
sudo tar xzvfC cilium-linux-${CLI_ARCH}.tar.gz /usr/local/bin
rm cilium-linux-${CLI_ARCH}.tar.gz{,.sha256sum}
```

### 2. نفعّل الـ Cilium Gateway API Controller باستخدام Helm

هنشغل Cilium ونديله شوية إعدادات خاصة:

```bash
helm upgrade cilium cilium/cilium --version 1.16.0 \
    --namespace kube-system \
    --reuse-values \
    --set kubeProxyReplacement=true \
    --set gatewayAPI.enabled=true
```

### 3. نعمل إعادة تشغيل لـ Cilium Operator وDaemonSet

عشان نطمن إن التغييرات اشتغلت تمام، نعمل إعادة تشغيل:

```bash
kubectl -n kube-system rollout restart deployment/cilium-operator
kubectl -n kube-system rollout restart ds/cilium
```

### 4. نطمن إن كل حاجة شغالة كويس

نتأكد من حالة الـ Cilium Agent وOperator:

```bash
cilium status
```

## تفعيل Host Network Mode

### 1. نشغل Host Network Mode

ده إعدادات عشان نفعّل Host Network Mode:

```yaml
gatewayAPI:
  enabled: true
  hostNetwork:
    enabled: true
```

### 2. نحدد منفذ الشبكة

نضبط المنفذ عشان نقدر ن Listen الحركة عليه:

```yaml
spec:
  listeners:
    - port: 8080
```

## فحص الموارد

### 1. نشوف موارد الـ Gateway

نستخدم الأمر دا عشان نجيب كل الـ Gateway الموجودين:

```bash
kubectl get gateway -A
```

### 2. نشوف موارد الـ HTTPRoute

نتأكد إن كل الـ HTTPRoute شغالين كويس:

```bash
kubectl get httproute -A
```

## المشاكل الشائعة وحلها

- **لو الـ Backend Service مش موجودة**:
  
  نشوف تفاصيل الـ HTTPRoute باستخدام:
  ```bash
  kubectl describe httproute <name>
  ```

- **لو الـ Gateway مش متعرف صح**:

  نستخدم نفس الأمر اللي فوق:
  ```bash
  kubectl describe httproute <name>
  ```

- **فحص سجلات الـ Cilium Operator**:

  نجيب اللوجات الخاصة بالـ Gateway من الـ Cilium Operator:
  ```bash
  kubectl logs -n kube-system deployments/cilium-operator | grep gateway
  ```



## الخلاصة

الـ Gateway API بيديك واجهة قوية لإدارة الترافيك في Kubernetes. باستخدام Cilium، تقدر تستفيد من كل الميزات دي وتزود أمان وإدارة الشبكات في الكلاستر بتاعك.

### خطوات إضافية للتحقق

📌 اتأكد من وجود الـ CRDs بعد تثبيتها:
```bash
kubectl get crd \
  gatewayclasses.gateway.networking.k8s.io \
  gateways.gateway.networking.k8s.io \
  httproutes.gateway.networking.k8s.io \
  referencegrants.gateway.networking.k8s.io \
  tlsroutes.gateway.networking.k8s.io \
  grpcroutes.gateway.networking.k8s.io
  ```

📌 تأكد إن Cilium متثبت ومفعّل بالـ Gateway API:
```bash
cilium config view | grep -w "enable-gateway-api"
```

---

طبعًا، هنزبطها بشكل جذاب مع الألوان والصواريخ:

---
📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍
### 🚀🚀🚀🚀🚀🚀🚀🚀🚀🚀🚀🥇 **Advanced Gateway API** 🚀🚀🚀🚀🚀🚀🚀🚀🚀🚀🚀
📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍
---

### 🚀 **استخدامات متقدمة لـ Gateway API**

قبل ما نقدر ننزل **Cilium** مع ميزة **Gateway API**، فيه شوية حاجات لازم نعملها الأول:

1. ال**Cilium** لازم يكون متكوّن بإعداد **kubeProxyReplacement** على **true**.
   - 🛠️ **ده معناه** إنك لازم تفعل خاصية الاستبدال بتاعت **kube-proxy** في **Cilium**، واللي بتحسّن الأداء وبتسهل إدارة الشبكة.

2. لازم يكون فيه **CRDs** من **Gateway API** متسطبة قبل كده.
   - 📋 **دي بتكون تعريفات** خاصة بالـ **Gateway API**، وبتبقى ضرورية قبل ما تنزل **Cilium**.

### 🔍 **ازاي نتأكد من وجود CRDs؟**

عشان نتأكد إن الموارد دي موجودة في الكلاستر بتاعك، استخدم الكوماند ده:

```bash
kubectl get crd \
  gatewayclasses.gateway.networking.k8s.io \
  gateways.gateway.networking.k8s.io \
  httproutes.gateway.networking.k8s.io \
  referencegrants.gateway.networking.k8s.io \
  tlsroutes.gateway.networking.k8s.io \
  grpcroutes.gateway.networking.k8s.io
```

#### 🛠️ **امتنساش تنصيب Cilium باستخدام الفلاجز دي:**

```bash
--set kubeProxyReplacement=true \
--set gatewayAPI.enabled=true
```

- 💡 **دي معناها** إنك فعلت خاصية الاستبدال بتاعت **kube-proxy** وفعلت مميزات **Gateway API** في **Cilium**.

### ✅ **ازاي نتأكد إن Cilium شغال صح؟**

استخدم الكوماند ده عشان تتأكد إن **Cilium** شغال مظبوط:

```bash
cilium status --wait
```

- ⏳ **الكوماند ده هيستنى** لحد ما **Cilium** يبقى شغال ويبلغك بحالته.

### 🧩 **ازاي نتأكد إن Cilium متفعل مع مميزات Gateway API؟**

تقدر تستخدم الكوماند ده عشان تشوف إذا كان **Cilium** متسطب مع مميزات **Gateway API**:

```bash
cilium config view | grep -w "enable-gateway-api "
```


📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍
### 🚀🚀🚀🚀🚀🚀🚀🚀🚀🚀🚀🥇 **Advanced Use Cases Gateway API** 🚀🚀🚀🚀🚀🚀🚀🚀🚀🚀🚀
📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍
---

#### 📌 نشر التطبيق التجريبي

1. **افتح ملف `echoserver.yml`:**

   ```yaml
   apiVersion: v1
   kind: Service
   metadata:
     labels:
       app: echo-1
     name: echo-1
   spec:
     ports:
     - port: 8080
       name: high
       protocol: TCP
       targetPort: 8080
     selector:
       app: echo-1
   ---
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     labels:
       app: echo-1
     name: echo-1
   spec:
     replicas: 1
     selector:
       matchLabels:
         app: echo-1
     template:
       metadata:
         labels:
           app: echo-1
       spec:
         containers:
         - image: gcr.io/kubernetes-e2e-test-images/echoserver:2.2
           name: echo-1
           ports:
           - containerPort: 8080
           env:
             - name: NODE_NAME
               valueFrom:
                 fieldRef:
                   fieldPath: spec.nodeName
             - name: POD_NAME
               valueFrom:
                 fieldRef:
                   fieldPath: metadata.name
             - name: POD_NAMESPACE
               valueFrom:
                 fieldRef:
                   fieldPath: metadata.namespace
             - name: POD_IP
               valueFrom:
                 fieldRef:
                   fieldPath: status.podIP
   ```

   - هنا بنحدد إزاي ننشر تطبيق تجريبي. 
   - هننشر بودز وخدمه اسمها `echo-1`.

2. **شغل الأمر:**

   ```bash
   kubectl apply -f echoserver.yml
   ```

   - ده هينشر التطبيق التجريبي على الكلاستر بتاعك.

---

#### 📌 نشر الـ Gateway والـ HTTPRoute

1. **افتح ملف `gateway.yaml`:**

   ```yaml
   apiVersion: gateway.networking.k8s.io/v1beta1
   kind: Gateway
   metadata:
     name: cilium-gw
   spec:
     gatewayClassName: cilium
     listeners:
     - protocol: HTTP
       port: 80
       name: web-gw-echo
       allowedRoutes:
         namespaces:
           from: Same
   ```

   - هنا بنحدد الـ Gateway اللي هتكون النقطة اللي الترافيك هيدخل منها.

2. **افتح ملف `http-route.yml`:**

   ```yaml
   apiVersion: gateway.networking.k8s.io/v1beta1
   kind: HTTPRoute
   metadata:
     name: example-route-1
   spec:
     parentRefs:
     - name: cilium-gw
     rules:
     - matches:
       - path:
           type: PathPrefix
           value: /echo
       backendRefs:
       - kind: Service
         name: echo-1
         port: 8080
   ```

   - هنا بنحدد إزاي الترافيك هيتوجه من الـ Gateway للخدمات.

3. **شغل الأمر لنشر الـ Gateway والـ HTTPRoute:**

   ```bash
   kubectl apply -f gateway.yaml -f http-route.yml
   ```

   - ده هينشر الـ Gateway والـ HTTPRoute على الكلاستر.

---

#### 📌 إنشاء خدمة LoadBalancer للـ Gateway

1. **عمل خدمة LoadBalancer:**

   - بعد ما ننشر الـ Gateway، Kubernetes هينشئ خدمة LoadBalancer جديدة تستقبل الترافيك من الخارج.

---

#### 📌 الحصول على عنوان IP للـ Gateway

1. **احصل على عنوان IP:**

   ```bash
   GATEWAY=$(kubectl get gateway cilium-gw -o jsonpath='{.status.addresses[0].value}')
   echo $GATEWAY
   ```

   - ده هياخد عنوان IP اللي اتعين للـ Gateway ويخزنه في متغير اسمه `GATEWAY`.

---

#### 📌 التحقق من توجيه الترافيك

1. **تأكد من التوجيه:**

   ```bash
   curl --fail -s http://$GATEWAY/echo
   ```

   - ده هيتأكد إن الـ Gateway بيوجه الترافيك بشكل صحيح للخدمات.

---

### ملخص

بعد ما تعمل الخطوات دي:
1. هتنشر البودز والخدمات للتطبيق التجريبي.
2. هتعمل Gateway جديد لتوجيه الترافيك الخارجي.
3. هتحدد HTTPRoute لتوجيه الترافيك من الـ Gateway للخدمات.
4. هتحصل على عنوان IP خارجي للـ Gateway.
5. هتتحقق من إن الـ Gateway بيوجه الترافيك بشكل صحيح للخدمات.

---


---




📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍
# 🚀 ** Gateway API Edit HTTP Headers**
📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍

## 🛠️ **نشر التطبيق التجريبي**

أول خطوة هنعملها هي نشر تطبيق تجريبي اسمه `echo-1`. هنبدأ بملف YAML اللي هيساعدنا نعمل الخدمة والبود الخاص بالتطبيق.

### 📌 **ملف `echoserver.yml`**

```yaml
---
apiVersion: v1
kind: Service
metadata:
  labels:
    app: echo-1
  name: echo-1
spec:
  ports:
  - port: 8080
    name: high
    protocol: TCP
    targetPort: 8080
  selector:
    app: echo-1
---
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: echo-1
  name: echo-1
spec:
  replicas: 1
  selector:
    matchLabels:
      app: echo-1
  template:
    metadata:
      labels:
        app: echo-1
    spec:
      containers:
      - image: gcr.io/kubernetes-e2e-test-images/echoserver:2.2
        name: echo-1
        ports:
        - containerPort: 8080
        env:
          - name: NODE_NAME
            valueFrom:
              fieldRef:
                fieldPath: spec.nodeName
          - name: POD_NAME
            valueFrom:
              fieldRef:
                fieldPath: metadata.name
          - name: POD_NAMESPACE
            valueFrom:
              fieldRef:
                fieldPath: metadata.namespace
          - name: POD_IP
            valueFrom:
              fieldRef:
                fieldPath: status.podIP
```

### 📋 **الخطوات:**

1. **نشر التطبيق:**
   ```bash
   kubectl apply -f echoserver.yml
   ```

   بعد ما تنفذ الكوماند ده:
   - Kubernetes هيبدأ ينشر البودز والخدمات الخاصة بـ `echo-1`.
   - البودز هتبدأ تتعمل وتشتغل.
   - الخدمات هتخلي البودز يتواصلوا مع بعض ومع العالم الخارجي.

## 🌐 **نشر Gateway و HTTPRoute**

المرحلة دي هنعمل فيها Gateway و HTTPRoute، اللي هيحددوا إزاي الترافيك هيتوجه للخدمات.

### 📌 **ملف `gateway.yaml`**

```yaml
---
apiVersion: gateway.networking.k8s.io/v1beta1
kind: Gateway
metadata:
  name: cilium-gw
spec:
  gatewayClassName: cilium
  listeners:
  - protocol: HTTP
    port: 80
    name: web-gw-echo
    allowedRoutes:
      namespaces:
        from: Same
```

### 📌 **ملف `http-route.yml`**

```yaml
---
apiVersion: gateway.networking.k8s.io/v1beta1
kind: HTTPRoute
metadata:
  name: example-route-1
spec:
  parentRefs:
  - name: cilium-gw
  rules:
  - matches:
    - path:
        type: PathPrefix
        value: /echo
    backendRefs:
    - kind: Service
      name: echo-1
      port: 8080
```

### 📋 **الخطوات:**

1. **نشر الـ Gateway و الـ HTTPRoute:**
   ```bash
   kubectl apply -f gateway.yaml -f http-route.yaml
   ```

   بعد ما تنفذ الكوماند ده:
   - Kubernetes هينشئ Gateway جديد و HTTPRoute جديدة.
   - الـ Gateway هيسمح للترافيك إنه يدخل للكلاستر.
   - الـ HTTPRoute هتحدد إزاي الترافيك اللي جاي للـ Gateway هيتوجه للخدمات الداخلية.

## 💥 **إنشاء خدمة LoadBalancer للـ Gateway**

بعد ما نعمل Gateway، Kubernetes هيعمل خدمة من نوع LoadBalancer عشان يستقبل الترافيك من خارج الكلاستر ويوجهه للخدمات الداخلية.

### 📋 **الخطوات:**

1. **الحصول على عنوان IP للـ Gateway:**
   ```bash
   GATEWAY=$(kubectl get gateway cilium-gw -o jsonpath='{.status.addresses[0].value}')
   echo $GATEWAY
   ```

   - الكوماند ده هيجيب لك عنوان الـ IP الخاص بالـ Gateway.

2. **التحقق من التوجيه:**
   ```bash
   curl --fail -s http://$GATEWAY/echo
   ```

   - هنا هتبعت طلب HTTP للـ IP الخارجي للـ Gateway.
   - الـ Gateway هيوجه الطلب للخدمة `echo-1`.

## 🛠️ **تعديل HTTP Headers**

🎯 لماذا نستخدم تعديل HTTP Headers؟
تعديل HTTP Headers مهم لعدة أسباب:

الأمان: إضافة هيدرز لتحسين الأمان، مثل هيدرز لتفادي هجمات XSS أو CSRF.
التتبع والتقارير: إضافة هيدرز خاصة لتتبع الطلبات وجمع البيانات التحليلية.
التوجيه: توجيه الطلبات بشكل مخصص بناءً على هيدرز محددة.
في هذه الحالة، نستخدم Cilium Gateway API لتعديل HTTP Headers في الطلبات التي تصل إلى خدمة معينة. ده بيسمح لنا بإضافة هيدرز جديدة أو تعديل الهيدرز الحالية بدون الحاجة لتعديل التطبيق نفسه.
هنستخدم Cilium Gateway API لتعديل HTTP Headers في الطلبات.

### 📌 **ملف `echo-header-http-route.yaml`**

```yaml
---
apiVersion: gateway.networking.k8s.io/v1beta1
kind: HTTPRoute
metadata:
  name: header-http-echo
spec:
  parentRefs:
  - name: cilium-gw
  rules:
  - matches:
    - path:
        type: PathPrefix
        value: /cilium-add-a-request-header
    filters:
    - type: RequestHeaderModifier
      requestHeaderModifier:
        add:
        - name: my-cilium-header-name
          value: my-cilium-header-value
    backendRefs:
    - name: echo-1
      port: 8080
```

### 📋 **الخطوات:**

1. **نشر التعديلات:**
   ```bash
   kubectl apply -f echo-header-http-route.yaml
   ```

2. **التحقق من التعديلات:**
   ```bash
   curl --fail -s http://$GATEWAY/cilium-add-a-request-header
   ```

   - هتلاحظ إن الهيدرز تم تعديلها في الطلبات الواردة.

## 👁️ **مراقبة الحركة باستخدام Hubble (اختياري)**

Hubble هي أداة لمراقبة حركة الترافيك في Cilium.

### 📋 **الخطوات:**

1. **Forward للمنفذ لـ Hubble:**
   ```bash
   cilium hubble port-forward &
   ```

2. **مراقبة الحركة:**
   ```bash
   hubble observe --http-path "/cilium-add-a-request-header"
   ```

   - هتلاحظ حركة الترافيك وتأكد من إن التعديلات اللي عملتها شغالة بشكل صحيح.

---

**ملخص:**
- نشرنا تطبيق تجريبي.
- أنشأنا Gateway و HTTPRoute لتوجيه الترافيك.
- استخدمنا LoadBalancer لاستقبال الترافيك الخارجي.
- قمنا بتعديل HTTP Headers باستخدام Cilium Gateway API.
- استخدمنا Hubble لمراقبة الحركة (اختياري).

---





📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍
### 🚀 ** Gateway API: Edit HTTP Response Headers** 🚀
📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍

### 📒 **تعديل الـ HTTP Response Headers: ليه وإزاي؟**

#### **إيه فايدة تعديل الـ HTTP Response Headers؟**

الـ HTTP Response Headers هي معلومات بيرجعها السيرفر بعد ما يستقبل الطلب. تعديلها بيكون مفيد في حالات معينة:

1. **إضافة/إزالة الكوكيز**:
   - **مثال**: لو عندك موقع بتقوم فيه بعمل "لوجين" للمستخدم، ممكن تضيف كوكيز تحتوي على معلومات تسجيل الدخول. ممكن نضيف أو نزيل كوكيز في الـ headers لتطبيقات معينة عشان نراقب أو نتبع المستخدمين.

2. **تمييز بين النسخ**:
   - **مثال**: لو عندك إصدارين من نفس التطبيق (واحد ثابت وواحد تجريبي)، ممكن تعدل الـ headers عشان يعرف الفونت إند إذا كان بيخاطب النسخة الثابتة أو التجريبية. ده بيساعده يعرض واجهات مختلفة أو يتعامل مع البيانات بشكل مختلف.

#### **إزاي نعدل الـ HTTP Response Headers؟**

هنا هنعمل مثال عملي لكيفية تعديل الـ HTTP Response Headers باستخدام Cilium Gateway API.

1. **تطبيق التعديلات:**

   احنا هنضيف تعديل معين للـ HTTP headers في الملف `response-header-modifier-http-route.yaml`:

   ```yaml
   ---
   apiVersion: gateway.networking.k8s.io/v1beta1
   kind: HTTPRoute
   metadata:
     name: response-header-modifier
   spec:
     parentRefs:
       - name: cilium-gw
     rules:
       - matches:
           - path:
               type: PathPrefix
               value: /multiple
         filters:
           - type: ResponseHeaderModifier
             responseHeaderModifier:
               add:
                 - name: X-Header-Add-1
                   value: header-add-1
                 - name: X-Header-Add-2
                   value: header-add-2
                 - name: X-Header-Add-3
                   value: header-add-3
         backendRefs:
           - name: echo-1
             port: 8080
   ```

   في المثال ده، إحنا بنضيف ثلاث هيدرز جديدة في الاستجابة:
   - `X-Header-Add-1` مع القيمة `header-add-1`
   - `X-Header-Add-2` مع القيمة `header-add-2`
   - `X-Header-Add-3` مع القيمة `header-add-3`

2. **تحقق من التعديلات:**

   بعد ما تطبق التعديلات، هتعمل التالي:

   - **استرجاع عنوان الـ Gateway:**
     ```bash
     GATEWAY=$(kubectl get gateway cilium-gw -o jsonpath='{.status.addresses[0].value}')
     echo $GATEWAY
     ```

   - **إرسال طلب HTTP للعنوان الخارجي:**
     ```bash
     curl --fail -s http://$GATEWAY/multiple
     ```

   - **تحقق من الـ Response Headers:**
     لو عايز تشوف التعديلات، استخدم:
     ```bash
     curl -v --fail -s http://$GATEWAY/multiple
     ```

 
     
   هتشوف إن الهيدرز الجديدة اللي ضفناها ظهرت في الاستجابة:
   ```
   < HTTP/1.1 200 OK
   < date: Mon, 05 Aug 2024 21:53:20 GMT
   < content-type: text/plain
   < server: envoy
   < x-envoy-upstream-service-time: 0
   < x-header-add-1: header-add-1
   < x-header-add-2: header-add-2
   < x-header-add-3: header-add-3
   ```

ده كله بيساعدك تضمن إن البيانات اللي بترجع من السيرفر بتكون مناسبة لاحتياجاتك، سواء لتتبع المستخدمين أو لتوفير معلومات إضافية للفرونت إند.

##HOL:-


الحل ده بيغطي تجربة تعديل الـ HTTP response headers باستخدام Cilium Gateway API. الفكرة هي إنك تقدر تعدل في الـ response headers بنفس الطريقة اللي ممكن تعدل بيها في الـ request headers، وده بيساعد في حاجات زي إضافة أو إزالة cookies لباك إند معين أو تمييز الـ backend إذا كان stable أو beta.

### الخطوات اللي تمت:

1. **تطبيق HTTPRoute لتعديل الـ Response Headers:**
 

     ```yaml
     apiVersion: gateway.networking.k8s.io/v1beta1
     kind: HTTPRoute
     metadata:
       name: response-header-modifier
     spec:
       parentRefs:
         - name: cilium-gw
       rules:
         - matches:
             - path:
                 type: PathPrefix
                 value: /multiple
           filters:
             - type: ResponseHeaderModifier
               responseHeaderModifier:
                 add:
                   - name: X-Header-Add-1
                     value: header-add-1
                   - name: X-Header-Add-2
                     value: header-add-2
                   - name: X-Header-Add-3
                     value: header-add-3
           backendRefs:
             - name: echo-1
               port: 8080
     ```

   - هنا الـ HTTPRoute مربوط بـ Gateway اسمها `cilium-gw`، والـ rule بتطبق تعديل في الـ headers لما الـ path يبتدي بـ `/multiple`.

2. **الحصول على عنوان الـ Gateway:**
   - استخدمت الأمر ده عشان أجيب الـ IP الخاص بالـ Gateway:

     ```bash
     GATEWAY=$(kubectl get gateway cilium-gw -o jsonpath='{.status.addresses[0].value}')
     echo $GATEWAY
     ```

   - الناتج كان IP: `172.18.255.200`.

3. **اختبار إرسال طلب HTTP للـ Gateway:**
   - استخدمت curl للتأكد من إنك تقدر تبعت طلب HTTP للـ Gateway:

     ```bash
     curl --fail -s http://$GATEWAY/multiple
     ```

   - النتيجة كانت معلومات عن البود المستهدفة `echo-1` زي اسمها، IP، والـ server details.

4. **عرض الـ Response Headers:**
   - استخدمت curl في وضع verbose عشان تشوف الـ response headers:

     ```bash
     curl -v --fail -s http://$GATEWAY/multiple
     ```

   - في الـ response headers لاحظت إضافة الـ headers اللي تم تعديلها باستخدام الـ HTTPRoute:
     ```
     < x-header-add-1: header-add-1
     < x-header-add-2: header-add-2
     < x-header-add-3: header-add-3
     ```

الخطوات دي بتوضح كيفية تعديل الـ response headers باستخدام Gateway API، وده بيديك مرونة أكتر في التحكم في الاستجابات الخاصة بالـ backend services. تقدر تستخدم المثال ده في سيناريوهات كتير زي التوجيه المشروط أو التخصيص للـ headers بناءً على الـ requests أو الـ backends.


-----------------------------------------------------------------------------------------------------
------------------------------------------------------------------------------------------------------

📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍
## 🚀 HTTP Traffic Mirroring Used by Cilium Gateway API 🚀
📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍
الميزة اللي بتقدمها الـ Cilium Gateway API في الـ "mirroring" هي إنك تقدر تنسخ الترافيك اللي رايح لسيرفر معين (backend) وتوجه نسخة منه لسيرفر تاني. دا بيبقى مفيد لو عايز تختبر نسخة جديدة من الخدمة أو عشان تحلل الترافيك وتعرف المشاكل. يعني لو عندك نسخة V1 شغالة وعملت V2 عايز تجربها، ممكن تروح نسخ الترافيك ليها عشان تتأكد إنها تمام.

#### 🛠️ الخطوات بالأمثلة:

1. **تعريف الـ Backends:**

 yq demo-app.yaml
```yaml
# Kubernetes Deployment and Service definitions for infra-backend-v1 and infra-backend-v2

---
apiVersion: v1
kind: Service
metadata:
  name: infra-backend-v1
  labels:
    app: infra-backend-v1
spec:
  selector:
    app: infra-backend-v1
  ports:
    - protocol: TCP
      port: 8080
      targetPort: 3000
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: infra-backend-v1
  labels:
    app: infra-backend-v1
spec:
  replicas: 1
  selector:
    matchLabels:
      app: infra-backend-v1
  template:
    metadata:
      labels:
        app: infra-backend-v1
    spec:
      containers:
        - name: infra-backend-v1
          image: gcr.io/k8s-staging-ingressconformance/echoserver:v20221109-7ee2f3e
          env:
            - name: POD_NAME
              valueFrom:
                fieldRef:
                  fieldPath: metadata.name
            - name: NAMESPACE
              valueFrom:
                fieldRef:
                  fieldPath: metadata.namespace
          resources:
            requests:
              cpu: 10m
---
apiVersion: v1
kind: Service
metadata:
  name: infra-backend-v2
  labels:
    app: infra-backend-v2
spec:
  selector:
    app: infra-backend-v2
  ports:
    - protocol: TCP
      port: 8080
      targetPort: 3000
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: infra-backend-v2
  labels:
    app: infra-backend-v2
spec:
  replicas: 1
  selector:
    matchLabels:
      app: infra-backend-v2
  template:
    metadata:
      labels:
        app: infra-backend-v2
    spec:
      containers:
        - name: infra-backend-v2
          image: gcr.io/k8s-staging-ingressconformance/echoserver:v20221109-7ee2f3e
          env:
            - name: POD_NAME
              valueFrom:
                fieldRef:
                  fieldPath: metadata.name
            - name: NAMESPACE
              valueFrom:
                fieldRef:
                  fieldPath: metadata.namespace
          resources:
            requests:
              cpu: 10m
```
📌root@server:~# kubectl get -f demo-app.yaml
NAME                       TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
service/infra-backend-v1   ClusterIP   10.96.203.120   <none>        8080/TCP   4m54s

NAME                               READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/infra-backend-v1   1/1     1            1           4m54s

NAME                       TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
service/infra-backend-v2   ClusterIP   10.96.247.211   <none>        8080/TCP   4m54s

NAME                               READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/infra-backend-v2   1/1     1            1           4m54s

2. **تفعيل الـ Mirroring:**
   - هنا هنضيف في الـ HTTPRoute الخاصية اللي هتنسخ الترافيك.
     

    ```yaml
    apiVersion: gateway.networking.k8s.io/v1beta1
    kind: HTTPRoute
    metadata:
      name: request-mirror
    spec:
      parentRefs:
      - name: cilium-gw
      rules:
      - matches:
        - path:
            type: PathPrefix
            value: /mirror
        filters:
         - type: RequestMirror
           requestMirror:
             backendRef:
               name: infra-backend-v2
               port: 8080
        backendRefs:
        - name: infra-backend-v1
          port: 8080
    ```
root@server:~# GATEWAY=$(kubectl get gateway cilium-gw -o jsonpath='{.status.addresses[0].value}')
echo $GATEWAY
172.18.255.200
root@server:~# 

📎Make a request to the gateway:

📌curl -s http://$GATEWAY/mirror | jq

📎Check the >_ 📜 Backend Logs tab. This tab watches the access logs for both infra-backend-v1 and infra-backend-v2 pods.

📌kubectl logs -f deploy/infra-backend-v1 
📌kubectl logs -f deploy/infra-backend-v2


🪞 Deploy the mirrored route
Using the </> Editor, edit the http-mirror-route.yaml manifest and uncomment the filters section (lines 14-19) in the manifest, then apply it in the >_ Terminal:

---
apiVersion: gateway.networking.k8s.io/v1beta1
kind: HTTPRoute
metadata:
  name: request-mirror
spec:
  parentRefs:
  - name: cilium-gw
  rules:
  - matches:
    - path:
        type: PathPrefix
        value: /mirror
    filters:
     - type: RequestMirror
       requestMirror:
         backendRef:
           name: infra-backend-v2
           port: 8080
    backendRefs:
    - name: infra-backend-v1
      port: 8080

📌kubectl apply -f http-mirror-route.yaml

📎Make a new request to the gateway:

curl -s http://$GATEWAY/mirror | jq

Has the mirroring actually happened?

Check the >_ 📜 Backend Logs tab again.

You will see logs on both sides of the split screen, showing that the traffic was indeed mirrored.

Press Check to move on to the next task, where you will be rewriting the HTTP URL.


الجزء ده في ملف الـ YAML بيستخدم لإعداد خاصية "mirroring" للترافيك باستخدام Cilium Gateway API. خلينا نشرح كل جزء بالتفصيل:

### الشرح

filters:
 - type: RequestMirror
   requestMirror:
     backendRef:
       name: infra-backend-v2
       port: 8080

- `filters:`:
  - القسم ده بيحدد الفلاتر اللي هتطبق على الترافيك الداخل. الفلاتر دي بتكون مسؤولة عن تعديل الترافيك، سواء بإضافة هيدر، أو إجراء تغييرات أخرى، أو حتى تنفيذ Mirroring.

- `type: RequestMirror`:
  - هنا بنحدد نوع الفلتر. النوع RequestMirror يعني أن الفلتر هيقوم بعمل "mirroring" للترافيك. بمعنى آخر، الترافيك اللي رايح لـ backend الأساسي هيتنسخ ويتوجه أيضاً إلى backend إضافي.

- `requestMirror:`:
  - تحت هذا القسم، بنحدد التفاصيل الخاصة بعملية المرايا.

- `backendRef:`:
  - ده يشير إلى الـ backend اللي هيستقبل نسخة من الترافيك. في الحالة دي، الترافيك هيكون موجه لـ infra-backend-v2 على المنفذ 8080.

- `name: infra-backend-v2`:
  - اسم الـ backend اللي هيستقبل نسخة من الترافيك. في الحالة دي، هو infra-backend-v2.

- `port: 8080`:
  - المنفذ اللي هيتم إرسال الترافيك عليه في الـ backend infra-backend-v2.

### الجزء التالي

backendRefs:
- name: infra-backend-v1
  port: 8080

- `backendRefs:`:
  - هنا بنحدد الـ backends اللي هيستقبلوا الترافيك الأصلي. الترافيك مش هيتوجه بس للـ backend اللي معمول له Mirroring، لكن كمان للـ backend الرئيسي.

- `name: infra-backend-v1`:
  - اسم الـ backend اللي هيتلقى الترافيك الأساسي. في الحالة دي، هو infra-backend-v1.

- `port: 8080`:
  - المنفذ اللي هيتم إرسال الترافيك عليه في الـ backend infra-backend-v1.

### في النهاية

الـ filters هنا بتعمل عملية "mirroring" للترافيك، بمعنى أن الترافيك اللي متوجه لـ infra-backend-v1 هيتنسخ ويروح كمان لـ infra-backend-v2. العملية دي مفيدة جداً لاختبار التغييرات أو لعمل تحليل للترافيك بدون التأثير على الـ backend الأساسي. 


3. **تجربة Mirroring:**
   - جبنا الـ IP الخاص بالـ Gateway.
   - بعدها بنعمل curl على الـ `/mirror` path.
   - هنشوف الترافيك بيتوجه للـ `infra-backend-v1` وكمان للـ `infra-backend-v2`.

    ```bash
    GATEWAY=$(kubectl get gateway cilium-gw -o jsonpath='{.status.addresses[0].value}')
    curl -s http://$GATEWAY/mirror | jq
    ```

4. **تشوف اللوجز:**
   - افتح اللوجز بتاعة الباك إند الأولاني (`infra-backend-v1`) والتاني (`infra-backend-v2`)، هتلاقي الترافيك متنسخ للاتنين.
   
    ```bash
    kubectl logs -f deploy/infra-backend-v1 
    kubectl logs -f deploy/infra-backend-v2
    ```

### 🤔 الفايدة من الموضوع ده:
- **تجربة حاجات جديدة**: تقدر تختبر خدمة جديدة من غير ما تأثر على المستخدمين.
- **تحليل الأداء والمشاكل**: تقدر تجمع بيانات وتعمل تحليلات من غير ما تأثر على الأصل.

يعني باختصار، حتة Mirroring دي جامدة عشان تقدر تجرب حاجات جديدة بأمان 🚀😎!

------------------------------------------------------------------------------------------------------------
---------------------------------------------------------------------------------------------------------------------------------------------------------
📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍
## 🚀HTTP URL Rewrite Used by Cilium Gateway API 🚀
📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍
💥🚀✨ **إعادة كتابة HTTP URL باستخدام Cilium Gateway API** ✨🚀💥

في التحدي اللي قدامنا ده، هنستخدم خاصية الـ **Cilium Gateway API** علشان نعيد كتابة الـ URL اللي بيستخدمه الترافيك في الـ HTTP. الفكرة ببساطة هي إننا نغير الـ URL اللي العميل بيطلبه قبل ما نوصله للخادم (الـ Backend) اللي عايزين نوصلهوله. الموضوع ده بيبقى ليه فوائد كتير، زي:

🚀 **ليه بنعمل إعادة كتابة للـ URL؟**
1. **توجيه الترافيك لمسار مختلف**: تخيل إن عندك أكتر من تطبيق وكل واحد على مسار مختلف، لو عايز تبعت الترافيك بتاع الـ URL معين لتطبيق معين، إعادة كتابة الـ URL هتساعدك توصل للهدف ده.
   
2. **إخفاء التفاصيل الداخلية للمسارات**: لو المسارات الداخلية للخوادم عندك مش زي اللي العميل بيشوفها، إعادة كتابة الـ URL بتساعدك إنك تخلي الأمور منظمة وتخفي التفاصيل الداخلية.

3. **تطبيق قواعد للتوجيه**: ممكن توجه الترافيك بناءً على شروط معينة، زي الباراميترز في الـ URL أو أي حاجة تانية.

✨ **إزاي بنعمل إعادة كتابة للـ URL؟**
هناخد مثال بسيط نشرح بيه الفكرة: 

هنعيد كتابة URL معين علشان يكون مناسب للـ backend بتاعنا:

📝 **خطوات**:

1. **إنشاء ملف الـ YAML**: هنبدأ نكتب إعدادات إعادة كتابة الـ URL في الملف ده.

    ```yaml
    ---
    apiVersion: gateway.networking.k8s.io/v1beta1
    kind: HTTPRoute
    metadata:
      name: rewrite-path
    spec:
      parentRefs:
        - name: cilium-gw
      rules:
        - matches:
            - path:
                type: PathPrefix
                value: /prefix/one
          filters:
            - type: URLRewrite
              urlRewrite:
                path:
                  type: ReplacePrefixMatch
                  replacePrefixMatch: /one
          backendRefs:
            - name: infra-backend-v1
              port: 8080
    ```

2. **تطبيق ملف الـ YAML باستخدام kubectl**:

    📌 أمر التطبيق:
    ```bash
    kubectl apply -f http-rewrite-route.yaml
    ```
    ✅ هتلاقي إنه عمل إنشاء للـ HTTPRoute اللي محتاجينه:
    ```
    httproute.gateway.networking.k8s.io/rewrite-path created
    ```

3. **اختبار إعادة كتابة الـ URL**:

    📌 نستخدم curl ونشوف النتيجة:
    ```bash
    GATEWAY=$(kubectl get gateway cilium-gw -o jsonpath='{.status.addresses[0].value}')
    curl -s http://$GATEWAY/prefix/one | jq
    ```

    ✅ النتيجة هتكون:
    ```json
    {
      "path": "/one",
      "host": "172.18.255.200",
      "method": "GET",
      "proto": "HTTP/1.1",
      "headers": {
        "Accept": [
          "*/*"
        ],
        "User-Agent": [
          "curl/8.5.0"
        ],
        "X-Envoy-Internal": [
          "true"
        ],
        "X-Envoy-Original-Path": [
          "/prefix/one"
        ],
        "X-Forwarded-For": [
          "172.18.0.1"
        ],
        "X-Forwarded-Proto": [
          "http"
        ],
        "X-Request-Id": [
          "a2bc4ea4-a1d9-453d-a9bd-18fe414352ba"
        ]
      },
      "namespace": "default",
      "ingress": "",
      "service": "",
      "pod": "infra-backend-v1-8558ddcc55-db2fb"
    }
    ```

🚀💥 بكده، الـ URL بيتغير من `/prefix/one` إلى `/one` زي ما عايزين، والترافيك بيروح للمكان الصحيح🚀💥

-------------------------------------------------------------------------------------------------------------------------------------------
-------------------------------------------------------------------------------------------------------------------------------------------
📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍
 ## 🚀HTTP Traffic Redirect Used by Cilium Gateway API 🚀
📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍
 🚀🚀🚀
---

📒📒📒📒📒📒📒📒📒📝 **إعادة توجيه ترافيك HTTP**  
في التحدي ده، هنحول الترافيك من URL لآخر باستخدام Cilium Gateway API. 

ممكن تتحكم في الـ path، والـ hostname، وكود إعادة التوجيه (زي 301 أو 302) في الرسائل بتاعتك.  
ده بيفيد في حالة التحويل المؤقت أو الدائم للتطبيق. 📒📒📒📒📒

---

## يعني إيه إعادة توجيه الترافيك HTTP؟

لما بنقول "إعادة توجيه الترافيك" (Redirect)، يعني إنك بتعمل تحويل لأي طلب (Request) جاي على رابط (URL) معين لرابط تاني. مثلا، لو عندك موقع قديم وعايز تحوّل الزوار لموقع جديد تلقائيًا. 🚀

### مثال بسيط 🎯

افترض إن عندك موقع ويب قديم على الرابط `/old-path`، وعايز تحول كل اللي بيزوروا الرابط ده لرابط جديد زي `/new-path`.  
ده هيتم عن طريق إعداد "إعادة توجيه" في Cilium Gateway API.

### خطوات الإعداد:

1. أول خطوة بتكون تطبيق الـ YAML file زي كده:

   ```bash
   root@server:~# kubectl apply -f redirect-route.yaml
   ```

   بعد ما تطبق الملف ده، هيظهرلك حاجة زي كده:

   ```bash
   httproute.gateway.networking.k8s.io/redirect-path created
   ```

2. نراجع محتوى الملف:

   ```bash
   root@server:~# yq redirect-route.yaml
   ```

   هتلاقي محتوى الـ YAML كالتالي:

```yaml
apiVersion: gateway.networking.k8s.io/v1beta1
kind: HTTPRoute
metadata:
  name: redirect-path
spec:
  parentRefs:
    - name: cilium-gw
  rules:
    - matches:
        - path:
            type: PathPrefix
            value: /original-prefix
      filters:
        - type: RequestRedirect
          requestRedirect:
            path:
              type: ReplacePrefixMatch
              replacePrefixMatch: /replacement-prefix
    - matches:
        - path:
            type: PathPrefix
            value: /path-and-host
      filters:
        - type: RequestRedirect
          requestRedirect:
            hostname: example.org
            path:
              type: ReplacePrefixMatch
              replacePrefixMatch: /replacement-prefix
    - matches:
        - path:
            type: PathPrefix
            value: /path-and-status
      filters:
        - type: RequestRedirect
          requestRedirect:
            path:
              type: ReplacePrefixMatch
              replacePrefixMatch: /replacement-prefix
            statusCode: 301
    - matches:
        - path:
            type: PathPrefix
            value: /scheme-and-host
      filters:
        - type: RequestRedirect
          requestRedirect:
            hostname: example.org
            scheme: "https"
```

3. هنشرح كل جزء من الـ YAML:

   - ال**apiVersion**: بتمثل نسخة الـ API اللي بنستخدمها لتكوين HTTPRoute.
   - ال**kind**: نوع المورد، هنا هو HTTPRoute.
   - ال**metadata**: معلومات زي اسم الـ HTTPRoute (هنا اسمه `redirect-path`).
   - ال**spec**: ده فيه تفاصيل التوجيه والقواعد.
   - ال**parentRefs**: بيحدد الـ Gateway اللي هيتطبق عليه القواعد (هنا `cilium-gw`).
   - ال**rules**: فيها القواعد اللي هتتطبق على الترافيك اللي جاي.

### القواعد بالتفصيل 📝:

**القاعدة الأولى:**
🔀 HTTP Path Redirect
```yaml
- matches:
  - path:
      type: PathPrefix
      value: /original-prefix
  filters:
  - type: RequestRedirect
    requestRedirect:
      path:
        type: ReplacePrefixMatch
        replacePrefixMatch: /replacement-prefix
```
```yaml
matches:
  path: بيطابق الطلبات اللي بتبدأ بـ /original-prefix.
filters:
  type: RequestRedirect: الفلتر ده بيعمل إعادة توجيه.
requestRedirect:
  path:
    type: ReplacePrefixMatch: بيستبدل البادئة /original-prefix بـ /replacement-prefix.
```

---

**القاعدة التانية:**
✨ Redirect to new hostname and new prefix
```yaml
- matches:
  - path:
      type: PathPrefix
      value: /path-and-host
  filters:
  - type: RequestRedirect
    requestRedirect:
      hostname: example.org
      path:
        type: ReplacePrefixMatch
        replacePrefixMatch: /replacement-prefix
```
```yaml
matches:
  path: بيطابق الطلبات اللي بتبدأ بـ /path-and-host.
filters:
  hostname: example.org: بيحوّل الطلبات لـ hostname جديد (example.org).
  path:
    type: ReplacePrefixMatch: بيستبدل البادئة /path-and-host بـ /replacement-prefix.
```

---

**القاعدة التالتة:**
🔢 Redirect - new status code and new prefix
```yaml
- matches:
  - path:
      type: PathPrefix
      value: /path-and-status
  filters:
  - type: RequestRedirect
    requestRedirect:
      path:
        type: ReplacePrefixMatch
        replacePrefixMatch: /replacement-prefix
      statusCode: 301
```
```yaml
matches:
  path: بيطابق الطلبات اللي بتبدأ بـ /path-and-status.
filters:
  path:
    type: ReplacePrefixMatch: بيستبدل البادئة /path-and-status بـ /replacement-prefix.
  statusCode: 301: ده معناه إن إعادة التوجيه هتكون برمز الحالة HTTP 301 (الانتقال الدائم).
```

---

**القاعدة الرابعة:**
🔒 Redirect - from HTTP to HTTPS and new prefix
```yaml
- matches:
  - path:
      type: PathPrefix
      value: /scheme-and-host
  filters:
  - type: RequestRedirect
    requestRedirect:
      hostname: example.org
      scheme: "https"
```
```yaml
matches:
  path: بيطابق الطلبات اللي بتبدأ بـ /scheme-and-host.
filters:
  hostname: example.org: بيحوّل الطلبات لـ hostname جديد (example.org).
  scheme: "https": بيحدد إن البروتوكول هيكون HTTPS.
``` 

---



### ملخص ⚡️:

الـ YAML ده بيحدد إزاي الطلبات اللي بتطابق شروط معينة هتتحول لروابط جديدة أو مضيفين جداد، وده ممكن يشمل تغيير البروتوكول أو كود الحالة. القواعد دي بتساعدك تدير تغييرات المواقع أو التطبيقات بشكل مرن وفعال.

---

دلوقتي ممكن نرجع IP الـ Gateway:

```bash
GATEWAY=$(kubectl get gateway cilium-gw -o jsonpath='{.status.addresses[0].value}')
echo $GATEWAY
```

---

🔀 **HTTP Path Redirect**  
خلينا نشوف الترافيك اللي مبني على الـ URL path بيتغير إزاي:

```bash
root@server:~# yq '.spec.rules[0]' redirect-route.yaml
```
```yaml
matches:
  - path:
      type: PathPrefix
      value: /original-prefix
filters:
  - type: RequestRedirect
    requestRedirect:
      path:
        type: ReplacePrefixMatch
        replacePrefixMatch: /replacement-prefix
```
بعد كده ممكن تعمل طلب HTTP للعنوان ده:

```bash
curl -l -v http://$GATEWAY/original-prefix
```

---

✨ **إعادة توجيه لhostname جديد وprefix جديد**  
اتأكد إن الترافيك بيبقى متوجه حسب التعديلات في HTTPRoute اللي أنشأناه:

```bash
root@server:~# yq '.spec.rules[1]' redirect-route.yaml
```

عملت طلب HTTP للعنوان ده:

```bash
curl -l -v http://$GATEWAY/path-and-host
```

### 301 **كود الحالة**

كود 301 (Moved Permanently) معناه إن المورد انتقل بشكل دائم. يعني العميل لازم يستخدم العنوان الجديد في المستقبل.

---

🔒 **إعادة توجيه من HTTP لـ HTTPS وprefix جديد**  
اتأكد من التوجيه حسب القواعد الموضحة في HTTPRoute:

```bash
root@server:~# yq '.spec.rules[3]' redirect-route.yaml
```
```yaml
matches:
  - path:
      type: PathPrefix
      value: /scheme-and-host
filters:
  - type: RequestRedirect
    requestRedirect:
      hostname: example.org
      scheme: "https"
```
عملت طلب HTTP للعنوان ده:

```bash
curl -l -v http://$GATEWAY/scheme-and-host
```

---

 😉🚀

-----------------------------------------------------------------------------------------------------------------------------------------
-----------------------------------------------------------------------------------------------------------------------------------------
📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍
 ## 🚀Cross Namespace Support Used by Cilium Gateway API 🚀
📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍

---

# 🌐 دعم التوجيه عبر الأسماء (Cross Namespace Support) 🌐

التوجيه عبر الأسماء في **Gateway API** بيسمحلك بربط التوجيهات بين Gateways وNamespaces في Kubernetes. الفكرة هنا هي التحكم المستقل بين أجزاء مختلفة من الكلاستر، مع إمكانية استخدام نفس Gateway إذا لزم الأمر. 

### 📋 ما هو Namespace؟

في Kubernetes، الـ Namespace هو تقسيم للموارد داخل الكلاستر، بيتيح لك فصل الموارد بين الفرق أو المشاريع المختلفة. كل مجموعة من الموارد ممكن تكون في Namespace مختلف.

### 🎯 ليه نحتاج (Cross Namespace Support)؟

1. **التحكم والأمان**:
   - الفرق المختلفة ممكن تحتاج تتحكم في موارد مختلفة أو قيود وصول مختلفة. باستخدام الأسماء، كل فريق ممكن يدير الموارد الخاصة به بدون تأثير على الآخرين.
2. **مشاركة البوابة (Gateway)**:
   - يمكن لعدة فرق استخدام نفس Gateway ولكن مع قواعد مختلفة بناءً على Namespace.

### 💡 كيف تعمل الفكرة؟

#### مثال:
افترض إن عندك فريقين: فريق A و فريق B، كل فريق في Namespace مختلف. وتريد أن يستخدموا نفس Gateway ولكن مع قواعد مختلفة.

---

### 🚀 السيناريو في شركة ACME

في شركة ACME، لدينا ثلاث فرق وكل فريق يعمل في Namespace خاص به:

1. **فريق التوظيف (Recruiting Team)**: يعمل على تطبيق ويب لعرض الوظائف وتقديم السيرة الذاتية.
2. **فريق المنتج (Product Team)**: يعمل على تطبيق ويب لعرض معلومات عن المنتجات.
3. **فريق الموارد البشرية (HR Team)**: يعمل على تطبيق داخلي لإدارة بيانات الموظفين.

#### استخدام Gateway API مشترك

ال**Gateway API** مسؤول عن توجيه الطلبات من الإنترنت إلى التطبيقات الداخلية. هنا، نريد استخدام Gateway مشترك للأشياء العامة فقط مثل تطبيقات التوظيف والمنتج، ولكن لا نريد أن يكون تطبيق الموارد البشرية (HR Team) متاحًا من الإنترنت.

---

### 🛠️ خطوات التنفيذ:

#### 1. إنشاء (Namespaces):
لدينا أربع مساحات أسماء:
- **infra-ns**
- **careers**
- **product**
- **hr**

لاحظ أن **careers** و **product** لديهم الملصق `shared-gateway-access=true`، بينما **hr** لا يحتوي على هذا Label.

#### 2. نشر الـ Gateway API و الـ HTTPRoutes:
نقوم بنشر الـ Gateway API فيNamespace اسم `infra-ns` بحيث يكون موصولاً مع HTTPRoutes من Namespace الأخرى.

```yaml
---
apiVersion: gateway.networking.k8s.io/v1beta1
kind: Gateway
metadata:
  name: shared-gateway
  namespace: infra-ns
spec:
  gatewayClassName: cilium
  listeners:
    - name: shared-http
      protocol: HTTP
      port: 80
      allowedRoutes:
        namespaces:
          from: Selector
          selector:
            matchLabels:
              shared-gateway-access: "true"
---
apiVersion: gateway.networking.k8s.io/v1beta1
kind: HTTPRoute
metadata:
  name: cross-namespace
  namespace: hr
spec:
  parentRefs:
    - name: shared-gateway
      namespace: infra-ns
  rules:
    - matches:
        - path:
            type: PathPrefix
            value: /hr
      backendRefs:
        - kind: Service
          name: echo-hr
          port: 9080
---
apiVersion: gateway.networking.k8s.io/v1beta1
kind: HTTPRoute
metadata:
  name: cross-namespace
  namespace: product
spec:
  parentRefs:
    - name: shared-gateway
      namespace: infra-ns
  rules:
    - matches:
        - path:
            type: PathPrefix
            value: /product
      backendRefs:
        - kind: Service
          name: echo-product
          port: 9080
---
apiVersion: gateway.networking.k8s.io/v1beta1
kind: HTTPRoute
metadata:
  name: cross-namespace
  namespace: careers
spec:
  parentRefs:
    - name: shared-gateway
      namespace: infra-ns
  rules:
    - matches:
        - path:
            type: PathPrefix
            value: /careers
      backendRefs:
        - kind: Service
          name: echo-careers
          port: 9080
```

---

### 📌 اختبار الـ HTTPRoutes:

أولاً، نحصل على عنوان IP للـ Gateway:

```bash
GATEWAY=$(kubectl get gateway shared-gateway -n infra-ns -o jsonpath='{.status.addresses[0].value}')
echo $GATEWAY
```

ثم نختبر الخدمات:
📎Now, let's connect to the product and careers Services:

```bash


📌root@server:~# curl -s -o /dev/null -w "%{http_code}\n" http://$GATEWAY/product

200

📌root@server:~# curl -s -o /dev/null -w "%{http_code}\n" http://$GATEWAY/careers

200
root@server:~#

📌root@server:~# curl -s -o /dev/null -w "%{http_code}\n" http://$GATEWAY/hr
404
root@server:~# 

📍It should return a 404. Why?

📍The HTTPRoute in the hr Namespace with a parentRef for infra-ns/shared-gateway would be ignored by the Gateway because the attachment constraint (Namespace label) was not met.

```

#### 📍 يجب أن تكون النتيجة كالتالي:

- ** product and careers**: 200
- **Hr**: 404

لماذا؟ لأن HTTPRoute في Namespace **hr** مع parentRef لـ `infra-ns/shared-gateway` سيتم تجاهله بواسطة الـ Gateway لأن شرط التثبيت (Namespace label) لم يتم تلبيته.

---

### 🔎 التحقق من حالة الـ HTTPRoutes:

#### 1. التحقق من حالة HTTPRoute لخدمة product  :

```bash
echo "Product HTTPRoute Status"
kubectl get httproutes.gateway.networking.k8s.io -n product -o jsonpath='{.items[0].status.parents[0].conditions[0]}' | jq
```
- ال`kubectl get httproutes.gateway.networking.k8s.io -n product`: يقوم هذا الأمر بجلب معلومات عن المسارات (HTTPRoutes) في مساحة اسم product.
- ال`-o jsonpath='{.items[0].status.parents[0].conditions[0]}'`: يحدد أننا نريد استخراج حالة أول مسار في القائمة. في الحالة دى،  نبحث عن حالة الاتصال بالـ Gateway.
- ال`| jq`: يقوم بتحليل النص الناتج بصيغة JSON لتنسيقه وعرضه بشكل أكثر وضوحًا.

النتيجة المتوقعة: يجب أن تكون الحالة "Accepted HTTPRoute"، مما يعني أن المسار مرتبط بالـ Gateway بشكل صحيح.

#### 2. التحقق من حالة HTTPRoute لخدمةcareers:

```bash
echo "Careers HTTPRoute Status"
kubectl get httproutes.gateway.networking.k8s.io -n careers -o jsonpath='{.items[0].status.parents[0].conditions[0]}' | jq
```

#### 3. التحقق من حالة HTTPRoute لخدمة hr:

```bash
echo "HR HTTPRoute Status"
kubectl get httproutes.gateway.networking.k8s.io -n hr -o jsonpath='{.items[0].status.parents[0].conditions[0]}' | jq
```

نتائج التحقق يجب أن تكون:
- **خدمة  product and careers**: "Accepted HTTPRoute"
- **خدمة hr**: "Rejected"

---

💬 🚀🌟
------------------------------------------------------------------------------------------------------------------
------------------------------------------------------------------------------------------------------------------
📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍
 ## 🚀⛕ Internal Layer 7 Traffic Management Used by Cilium Gateway API 🚀
East-West traffic
📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍
بالطبع، سأشرح لك الـ YAML files الخاصة بـ Kubernetes بطريقة مفصلة وباللغة العامية المصرية، وسأستخدم الإيموجي لتوضيح الأمور بشكل أفضل.

---

## **تعريف الـ YAML Files**

### 1. **تعريف Namespace**

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: gamma
```


---

### 2. **تعريف Deployment للنسخة الأولى من التطبيق (`echo-v1`)**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: echo-v1
  namespace: gamma
  labels:
    app: echo
spec:
  selector:
    matchLabels:
      app: echo
      version: v1
  template:
    metadata:
      labels:
        app: echo
        version: v1
    spec:
      containers:
        - name: echo
          image: gcr.io/k8s-staging-gateway-api/echo-advanced:v20240412-v1.0.0-394-g40c666fd
          imagePullPolicy: IfNotPresent
          args:
            - --tcp=9090
            - --port=8080
            - --grpc=7070
            - --port=8443
            - --tls=8443
            - --crt=/cert.crt
            - --key=/cert.key
```


---

### 3. **تعريف Service للنسخة الأولى من التطبيق (`echo-v1`)**

```yaml
apiVersion: v1
kind: Service
metadata:
  name: echo-v1
  namespace: gamma
spec:
  selector:
    app: echo
    version: v1
  ports:
    - name: http
      port: 80
      appProtocol: http
      targetPort: 8080
    - name: http-alt
      port: 8080
      appProtocol: http
    - name: https
      port: 443
      targetPort: 8443
    - name: tcp
      port: 9090
    - name: grpc
      port: 7070
      appProtocol: grpc
```

🔌 ال**Service** هو وسيلة للوصول للـ Pods من خارجهم أو من داخل الكلاستر. هنا، بنعرف Service باسم `echo-v1` اللي بيوجه الطلبات للـ Pods الخاصة بـ `echo-v1`.


---

### 4. **تعريف Deployment للنسخة الثانية من التطبيق (`echo-v2`)**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: echo-v2
  namespace: gamma
  labels:
    app: echo
spec:
  selector:
    matchLabels:
      app: echo
      version: v2
  template:
    metadata:
      labels:
        app: echo
        version: v2
    spec:
      containers:
        - name: echo
          image: gcr.io/k8s-staging-gateway-api/echo-advanced:v20240412-v1.0.0-394-g40c666fd
          imagePullPolicy: IfNotPresent
          args:
            - --tcp=9090
            - --port=8080
            - --grpc=7070
            - --port=8443
            - --tls=8443
            - --crt=/cert.crt
            - --key=/cert.key
```

🛠️ ال**Deployment** هنا مشابه للـ Deployment للنسخة الأولى، لكن بنستخدم النسخة الثانية من التطبيق `echo-v2`.

---

### 5. **تعريف Service للنسخة الثانية من التطبيق (`echo-v2`)**

```yaml
apiVersion: v1
kind: Service
metadata:
  name: echo-v2
  namespace: gamma
spec:
  selector:
    app: echo
    version: v2
  ports:
    - name: http
      port: 80
      appProtocol: http
      targetPort: 8080
    - name: http-alt
      port: 8080
      appProtocol: http
    - name: https
      port: 443
      targetPort: 8443
    - name: tcp
      port: 9090
    - name: grpc
      port: 7070
      appProtocol: grpc
```

🔌 ال**Service** هنا مشابه للـ Service للنسخة الأولى، لكن موجهة للنسخة الثانية من التطبيق `echo-v2`.

---

### 6. **تعريف Service عام (`echo`)**

```yaml
apiVersion: v1
kind: Service
metadata:
  name: echo
  namespace: gamma
spec:
  selector:
    app: echo
  ports:
    - name: http
      port: 80
      appProtocol: http
      targetPort: 8080
    - name: http-alt
      port: 8080
      appProtocol: http
    - name: https
      port: 443
      targetPort: 8443
    - name: tcp
      port: 9090
    - name: grpc
      port: 7070
      appProtocol: grpc
```

🔌 ال**Service** هنا بتكون شاملة، يعني بتوجه الطلبات لأي Pods تحمل `app: echo`، سواء كانت النسخة `v1` أو `v2`.

---

### 7. **تعريف Pod لـ Client**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: client
  namespace: gamma
spec:
  containers:
    - name: netshoot
      image: nicolaka/netshoot:latest
      command: ["sleep", "infinite"]
```

📦 ال**Pod** ده عبارة عن جهاز اختبار، وبيحتوي على حاوية بتقوم بتنفيذ أمر `sleep` بشكل دائم. ممكن تستخدمه لاختبار الشبكة أو الاتصال بالخدمات.

---

### 8. **تعريف HTTPRoute**

```yaml
apiVersion: gateway.networking.k8s.io/v1
kind: HTTPRoute
metadata:
  name: gamma-route
  namespace: gamma
spec:
  parentRefs:
    - group: ""
      kind: Service
      name: echo
  rules:
    - matches:
        - path:
            type: Exact
            value: /v1
      backendRefs:
        - name: echo-v1
          port: 80
    - matches:
        - path:
            type: Exact
            value: /v2
      backendRefs:
        - name: echo-v2
          port: 80
```
##طيب عاوز اعمل LB على الاتنين services دول:
-  ال LB يتم بشكل متساوي بين echo-v1 و echo-v2 لأن الوزن 50% لكل منهما.
```yaml
apiVersion: gateway.networking.k8s.io/v1beta1
kind: HTTPRoute
metadata:
  name: load-balancing-route
  namespace: gamma
spec:
  parentRefs:
    - group: ""
      kind: Service
      name: echo
  rules:
    - matches:
        - path:
            type: PathPrefix
            value: /load-balancing
      backendRefs:
        - kind: Service
          name: echo-v1
          port: 80
          weight: 50
        - kind: Service
          name: echo-v2
          port: 80
          weight: 50
```
🌐 ال**HTTPRoute** ده بيحدد كيفية توجيه الطلبات داخل الكلاستر بناءً على المسارات. في المثال ده، الطلبات على المسار `/v1` بتروح للنسخة `echo-v1`، والطلبات على المسار `/v2` بتروح للنسخة `echo-v2`.

---
📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍
🚪 Load-Balance East-West
## **LoadBalancing**
📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍
##طيب عاوز اعمل LB على الاتنين services دول:
-  ال LB يتم بشكل متساوي بين echo-v1 و echo-v2 لأن الوزن 50% لكل منهما.
```yaml
apiVersion: gateway.networking.k8s.io/v1beta1
kind: HTTPRoute
metadata:
  name: load-balancing-route
  namespace: gamma
spec:
  parentRefs:
    - group: ""
      kind: Service
      name: echo
  rules:
    - matches:
        - path:
            type: PathPrefix
            value: /load-balancing
      backendRefs:
        - kind: Service
          name: echo-v1
          port: 80
          weight: 50
        - kind: Service
          name: echo-v2
          port: 80
          weight: 50

```

 ### 3. التحقق من LB

الأمر:
kubectl -n gamma exec -it client -- bash -c '
for _ in {1..500}; do
  curl -s -k "http://echo/load-balancing" >> curlresponses.txt;
done
grep -o "Hostname=echo-v1" curlresponses.txt | sort | uniq -c
grep -o "Hostname=echo-v2" curlresponses.txt | sort | uniq -c
'

ما يفعله:
- يقوم هذا السكربت بإرسال 500 طلب إلى /load-balancing من  client ويجمع الاستجابات في ملف curlresponses.txt.
- بعد ذلك، يستخدم الأمر grep لحساب عدد الاستجابات التي جاءت من echo-v1 و echo-v2.

نتائج التحقق:
- تحقق من أن الاستجابات موزعة بشكل متساوي بين echo-v1 و echo-v2. هذا يؤكد أن توازن الحمل يعمل كما هو متوقع.

### ملخص

 هنا نستخدم HTTPRoute لتوجيه حركة المرور داخل الكلاستر (East-West) وتوزيعها بالتساوي بين خدمات متعددة باستخدام الوزن المحدد في التكوين.
 
📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍
#🔢 90/10 Traffic Split
This time, we will be applying a different weight.
📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍📍
### 1. تطبيق تكوين الـ HTTPRoute

**الأمر:**
```bash
kubectl apply -f load-balancing-http-route.yaml
```

**ما يفعله:**
- يقوم هذا الأمر بإنشاء أو تحديث التكوينات الخاصة بالـ HTTPRoute، التي توجه حركة المرور إلى خدمات مختلفة بناءً على مسار URL.

### 2. مراجعة تكوين الـ HTTPRoute

**الأمر:**
```bash
yq load-balancing-http-route.yaml
```

**ما يفعله:**
- يعرض محتويات ملف التكوين `load-balancing-http-route.yaml` بتنسيق YAML.

**التكوين المتوقع:**
```yaml
apiVersion: gateway.networking.k8s.io/v1beta1
kind: HTTPRoute
metadata:
  name: load-balancing-route
  namespace: gamma
spec:
  parentRefs:
    - group: ""
      kind: Service
      name: echo
  rules:
    - matches:
        - path:
            type: PathPrefix
            value: /load-balancing
      backendRefs:
        - kind: Service
          name: echo-v1
          port: 80
          weight: 50
        - kind: Service
          name: echo-v2
          port: 80
          weight: 50
```

**شرح التكوين:**
- `apiVersion: gateway.networking.k8s.io/v1beta1`: يحدد إصدار API المستخدم.
- `kind: HTTPRoute`: نوع المورد، هنا هو HTTPRoute.
- `metadata`:
  - `name: load-balancing-route`: اسم المورد.
  - `namespace: gamma`: النطاق الذي ينتمي إليه المورد.
- `spec`:
  - `parentRefs`:
    - `group: ""`: مجموعة فارغة لأننا نتعامل مع خدمة أساسية.
    - `kind: Service`: نوع المورد الذي ستطبق عليه القواعد.
    - `name: echo`: اسم الخدمة التي ستوجه إليها حركة المرور.
  - `rules`:
    - `matches`:
      - `path`:
        - `type: PathPrefix`: يطابق المسارات التي تبدأ بـ `/load-balancing`.
        - `value: /load-balancing`: المسار الذي سيتم توجيه الطلبات بناءً عليه.
    - `backendRefs`:
      - `name: echo-v1`: اسم الخدمة الأولى.
        - `port: 80`: المنفذ الذي ستوجه إليه حركة المرور.
        - `weight: 50`: الوزن المخصص لهذه الخدمة (50%).
      - `name: echo-v2`: اسم الخدمة الثانية.
        - `port: 80`: المنفذ الذي ستوجه إليه حركة المرور.
        - `weight: 50`: الوزن المخصص لهذه الخدمة (50%).

**لاحظ:**
- توازن الحمل يتم بشكل متساوي بين `echo-v1` و `echo-v2` لأن الوزن 50% لكل منهما.

### 3. التحقق من توازن الحمل

**الأمر:**
```bash
kubectl -n gamma exec -it client -- bash -c '
for _ in {1..500}; do
  curl -s -k "http://echo/load-balancing" >> curlresponses.txt;
done
grep -o "Hostname=echo-v1" curlresponses.txt | sort | uniq -c
grep -o "Hostname=echo-v2" curlresponses.txt | sort | uniq -c
'
```

**ما يفعله:**
- يقوم هذا السكربت بإرسال 500 طلب إلى `/load-balancing` من الحاوية `client` ويجمع الاستجابات في ملف `curlresponses.txt`.
- بعد ذلك، يستخدم الأمر `grep` لحساب عدد الاستجابات التي جاءت من `echo-v1` و `echo-v2`.

**نتائج التحقق:**
- تحقق من أن الاستجابات موزعة بشكل متساوي بين `echo-v1` و `echo-v2`. هذا يؤكد أن توازن الحمل يعمل كما هو متوقع.

### ملخص

نستخدم `HTTPRoute` لتوجيه حركة المرور داخل الكلاستر (East-West) وتوزيعها بالتساوي بين خدمات متعددة باستخدام الوزن المحدد في التكوين.

———————————————————————————————————————————————————

### 🔢 90/10 Traffic Split

في هذا الجزء، نريد تعديل إعدادات LB لتكون بنسبة 90/10 بدلاً من النسبة المتساوية 50/50. سنستخدم نفس المكونات، ولكن مع تغييرات في الوزن.

### 1. تعديل إعدادات LB

**المطلوب:**
- تعديل weight بين `echo-v1` و `echo-v2` لتكون 90/10 بدلاً من 50/50.

**الأمر:**
```bash
kubectl -n gamma edit httproutes load-balancing-route
```

**ما يفعله:**
- يفتح المحرر `vi` لتحرير تكوين `HTTPRoute` مباشرة على خادم API.

**التعديلات:**
- ابحث عن الأسطر التي تحدد الوزن في قسم `backendRefs`:
    ```yaml
    backendRefs:
    - kind: Service
      name: echo-v1
      port: 80
      weight: 50
    - kind: Service
      name: echo-v2
      port: 80
      weight: 50
    ```

- قم بتغييرweight إلى:
    ```yaml
    backendRefs:
    - kind: Service
      name: echo-v1
      port: 80
      weight: 90
    - kind: Service
      name: echo-v2
      port: 80
      weight: 10
    ```

 ```

### 2. إعادة اختبار LB

**الأمر:**
```bash
kubectl -n gamma exec -it client -- bash -c '
for _ in {1..500}; do
  curl -s -k "http://echo/load-balancing" >> curlresponses9010.txt;
done
grep -o "Hostname=echo-v1" curlresponses9010.txt | sort | uniq -c
grep -o "Hostname=echo-v2" curlresponses9010.txt | sort | uniq -c
'
```

**ما يفعله:**
- يقوم هذا السكربت بإرسال 500 طلب إلى `/load-balancing` وتخزين الاستجابات في ملف `curlresponses9010.txt`.
- بعد ذلك، يستخدم `grep` لحساب عدد الاستجابات التي جاءت من `echo-v1` و `echo-v2`.

**النتائج المتوقعة:**
- تحقق من أن الاستجابات موزعة بشكل تقريبي بنسبة 90% إلى `echo-v1` و 10% إلى `echo-v2`. هذا يؤكد أن توازن الحمل يتم بشكل صحيح وفقًا للوزن الجديد.
