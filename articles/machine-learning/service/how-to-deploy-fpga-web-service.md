---
title: A modell telepítése az Azure Machine Learning segítségével egy FPGA webszolgáltatásként
description: Útmutató az Azure Machine Learning segítségével FPGA futó modell egy webszolgáltatás-bővítmény telepítése.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: tedway
author: tedway
ms.date: 05/07/2018
ms.openlocfilehash: f3237980a1ad1969b5cf8d42d547ddf96608dd97
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="deploy-a-model-as-a-web-service-on-an-fpga-with-azure-machine-learning"></a>A modell rendszerbe állítása az Azure Machine Learning segítségével egy FPGA webszolgáltatásként

Ebből a dokumentumból megismerheti, hogyan állítsa be a munkaállomás-környezetet, és telepítette a modellek webszolgáltatásként [programozható kapu tömbök (FPGA) mezőben](concept-accelerate-with-fpgas.md). A webszolgáltatás a modell futtathatnak FPGA projekt Brainwave használja.

FPGAs segítségével biztosítja a leginkább kis késleltetésű inferencing egyetlen kötegméret mellett is.

## <a name="create-an-azure-machine-learning-model-management-account"></a>Azure Machine Learning modell Management-fiók létrehozása

1. Nyissa meg a modell felügyeleti fiókja létrehozásának oldalán a [Azure Portal](https://aka.ms/aml-create-mma).

2. A modell felügyeleti fiók létrehozása a portálon, a **USA keleti régiója 2** régióban.

   ![A modell felügyeleti fiók létrehozása képernyő](media/how-to-deploy-fpga-web-service/azure-portal-create-mma.PNG)

3. Nevezze el a modell felügyeleti fiókja, válasszon egy előfizetést, és válasszon egy erőforráscsoportot.

   >[!IMPORTANT]
   >A helyen, ki kell választania **USA keleti régiója 2** , a régiót.  Nincs más régiókban jelenleg érhetők el.

4. Válasszon egy tarifacsomagot (S1 megfelelő, de S2 és S3 is használható).  A DevTest szint nem támogatott.  

5. Kattintson a **válasszon** az árképzési szint megerősítéséhez.

6. Kattintson a **létrehozása** a ML-modell felügyeleti a bal oldalon.

## <a name="get-model-management-account-information"></a>Modell felügyeleti fiók adatainak lekérése

Ahhoz, hogy a modell felügyeleti fiók (MMA) kapcsolatos információkat, kattintson a __modell felügyeleti fiókja__ az Azure portálon.

Másolja az alábbi elemeket:

+ Felügyeleti fiókja modellnév (a bal felső sarokban lévő)
+ Erőforráscsoport neve
+ Előfizetés azonosítója
+ Hely (használata "eastus2")

![Minta felügyeleti fiókjának adatait](media/how-to-deploy-fpga-web-service/azure-portal-mma-info.PNG)

## <a name="set-up-your-machine"></a>A számítógép beállítása

A munkaállomás FPGA telepítéshez beállításához tegye a következőket:

1. Töltse le és telepítse a legújabb [Git](https://git-scm.com/downloads).

2. Telepítés [Anaconda (Python 3.6)](https://conda.io/miniconda.html).

3. Töltse le a Anaconda-környezet, használja a következő parancsot a Git parancssorba:

    ```
    git clone https://aka.ms/aml-real-time-ai
    ```

4. A környezet létrehozásához nyissa meg egy **Anaconda kérdés** (nem az Azure Machine Learning-munkaterület kérdés), és futtassa a következő parancsot:

    > [!IMPORTANT]
    > A `environment.yml` fájl van-e az előző lépésben klónozott git-tárházba. Az elérési utat módosítsa, szükség esetén a munkaállomáson fájlra mutasson.

    ```
    conda env create -f environment.yml
    ```

5. A környezet aktiválásához a következő paranccsal:

    ```
    conda activate amlrealtimeai
    ```

6. A Jupyter Notebook kiszolgáló indításához használja a következő parancsot:

    ```
    jupyter notebook
    ```

    Ez a parancs eredménye az alábbihoz hasonló:

    ```text
    Copy/paste this URL into your browser when you connect for the first time, to login with a token:
        http://localhost:8888/?token=bb2ce89cc8ae931f5df50f96e3a6badfc826ff4100e78075
    ```

    > [!TIP]
    > Egy másik tokent kap minden alkalommal, amikor a parancsot futtatja.

    Ha a böngésző nem automatikusan nyissa meg a Jupyter notebook, használja a HTTP URL-cím, az előző parancs által visszaadott lapjának megnyitásához.

    ![A Jupyter Notebook webes lapjának képe](./media/how-to-deploy-fpga-web-service/jupyter-notebook.png)

## <a name="deploy-your-model"></a>A modell rendszerbe állítása

A Jupyter Notebook nyissa meg a `00_QuickStart.ipynb` a notebook a `notebooks/resnet50` könyvtár. Kövesse a notebook használatához:

* A megadása
* A modell üzembe helyezése
* A telepített modell felhasználása
* Törölje a telepített szolgáltatások

> [!IMPORTANT]
> A késleltetés és a teljesítmény optimalizálása érdekében a munkaállomás és a végpont Azure ugyanabban a régióban kell lennie.  Jelenleg az API-k jönnek létre a keleti Velünk Azure azon régióját.

## <a name="ssltls-and-authentication"></a>SSL/TLS- és hitelesítési

Az Azure Machine Learning biztosít az SSL használatához és a kulcs-alapú hitelesítés. Ez lehetővé teszi, hogy a szolgáltatás és az ügyfelek által elküldött védett adatok elérésének korlátozása.

> [!NOTE]
> A jelen szakaszban szereplő lépéseket csak az Azure Machine Learning hardver gyorsított modellek vonatkoznak. A szabványos Azure Machine Learning-szolgáltatásokra, tekintse meg a [beállítása az Azure Machine Learning számítási SSL](https://docs.microsoft.com/azure/machine-learning/preview/how-to-setup-ssl-on-mlc) dokumentum.

> [!IMPORTANT]
> Hitelesítési szolgáltatások, amelyek egy SSL-tanúsítvány és kulcs megadott csak engedélyezve van. 
>
> Ha nem engedélyezi az SSL, az interneten lévő bármely felhasználó fog tudni hívásokat a szolgáltatáshoz.
>
> Ha engedélyezi az SSL és a hitelesítési kulcs, ha a szolgáltatás elérésével.

SSL titkosítja az ügyfél és a szolgáltatás között küldött adatokat. Ez a kiszolgáló identitásának ellenőrzése az ügyfél által is használt.

Szolgáltatás telepítése SSL engedélyezve van, vagy engedélyezheti egy már telepített szolgáltatásnak a frissítésére. A lépései megegyeznek:

1. Szerezzen be egy nevet.

2. Szerezzen be egy SSL-tanúsítványt.

3. Vagy frissítse a szolgáltatás az SSL engedélyezett.

4. A DNS, mutasson a szolgáltatás frissítése.

### <a name="acquire-a-domain-name"></a>Szerezzen be egy nevet

Ha nincs saját tartományneve, vásárolhat egyet egy __regisztrációs__. A folyamat eltérnek a regisztráló szervezetek, mint a költségeket. A regisztráló is használható eszközöket biztosít a tartománynév kezeléséhez. Ezek az eszközök az IP-cím, amely a szolgáltatás a segítségével történő leképezéséhez (például www.contoso.com) teljesen minősített tartománynevét.

### <a name="acquire-an-ssl-certificate"></a>Szerezzen be egy SSL-tanúsítvány

Számos módon SSL-tanúsítvány beszerzése. A leggyakoribb, hogy vásárolhat egyet egy __hitelesítésszolgáltató__ (CA). Ha beszerezte a tanúsítványt, függetlenül kell a következő fájlokat:

* A __tanúsítvány__. A tanúsítványnak tartalmaznia kell a teljes tanúsítványláncot, és PEM-kódolású kell lennie.
* A __kulcs__. A kulcsnak kell lennie a PEM-kódolású.

> [!TIP]
> Ha a hitelesítésszolgáltató nem ad meg a tanúsítványt és kulcsot PEM-kódolású fájlként, a segédprogram használható például [OpenSSL](https://www.openssl.org/) formátumának módosításakor.

> [!IMPORTANT]
> Önaláírt tanúsítványokat csak fejlesztési használható. Ezek nem használandó éles környezetben.
>
> Ha egy önaláírt tanúsítványt használ, tekintse meg a [fel azokat a szolgáltatásokat, önaláírt tanúsítványokat](#self-signed) adott részben.

> [!WARNING]
> A tanúsítvány igénylésekor a teljesen minősített tartománynevét (FQDN) kell megadnia a cím, a szolgáltatás használatát tervezi. Például www.contoso.com. A megjelölve a tanúsítvánnyal történő címet és az ügyfelek által használt cím képest, a szolgáltatás identitásának érvényesítése során.
>
> Ha a címek nem egyeznek, akkor az ügyfelek egy hibaüzenetet fog kapni. 

### <a name="deploy-or-update-the-service-with-ssl-enabled"></a>Vagy frissítse a szolgáltatás az SSL engedélyezve

SSL engedélyezve van a szolgáltatás telepítéséhez állítsa be a `ssl_enabled` paramétert `True`. Állítsa be a `ssl_certificate` paraméter értékének a __tanúsítvány__ fájl és a `ssl_key` értékének a __kulcs__ fájlt. A következő példa bemutatja, hogy a szolgáltatás telepítéséhez SSL engedélyezve:

```python
from amlrealtimeai import DeploymentClient

subscription_id = "<Your Azure Subscription ID>"
resource_group = "<Your Azure Resource Group Name>"
model_management_account = "<Your AzureML Model Management Account Name>"
location = "eastus2"

model_name = "resnet50-model"
service_name = "quickstart-service"

deployment_client = DeploymentClient(subscription_id, resource_group, model_management_account, location)

with open('cert.pem','r') as cert_file:
    with open('key.pem','r') as key_file:
        cert = cert_file.read()
        key = key_file.read()
        service = deployment_client.create_service(service_name, model_id, ssl_enabled=True, ssl_certificate=cert, ssl_key=key)
```

A válasz a `create_service` művelet a szolgáltatás az IP-címét tartalmazza. Az IP-cím akkor használatos, ha a leképezés a szolgáltatás az IP-címét a DNS-név.

A válasz is tartalmaz egy __elsődleges kulcs__ és __másodlagos kulcs__ felhasználásához, a szolgáltatás használt.

### <a name="update-your-dns-to-point-to-the-service"></a>A DNS, mutasson a szolgáltatás frissítése

A név tartományregisztrálónál által biztosított eszközök segítségével a tartomány nevét a DNS-rekordot frissíteni. A rekord a szolgáltatás az IP-címét kell mutatnia.

> [!NOTE]
> Attól függően, hogy a regisztráló és az idő-Élettartam (TTL) beállítva a tartománynév, ügyfelek tudja oldani a tartománynév előtt több órát is igénybe vehet néhány percig.

### <a name="consuming-authenticated-services"></a>Hitelesített szolgáltatások felhasználása

Az alábbi példák bemutatják, hogyan lehet használni a Python és a C# segítségével hitelesített szolgáltatásnak:

> [!NOTE]
> Cserélje le `authkey` az elsődleges vagy másodlagos kulcsot és értéket adott vissza a szolgáltatás.

```python
from amlrealtimeai import PredictionClient
client = PredictionClient(service.ipAddress, service.port, use_ssl=True, access_token="authKey")
image_file = R'C:\path_to_file\image.jpg'
results = client.score_image(image_file)
```

```csharp
var client = new ScoringClient(host, 50051, useSSL, "authKey");
float[,] result;
using (var content = File.OpenRead(image))
    {
        IScoringRequest request = new ImageRequest(content);
        result = client.Score<float[,]>(request);
    }
```

Más gRPC ügyfelek úgy, hogy egy engedélyezési fejléc kérések hitelesítése. Az általános módszer az, hogy hozzon létre egy `ChannelCredentials` objektum, amely egyesíti az `SslCredentials` rendelkező `CallCredentials`. Ez a kérés hitelesítési fejlécéhez kerül. A megadott fejlécek támogatása végrehajtási további információkért lásd: [ https://grpc.io/docs/guides/auth.html ](https://grpc.io/docs/guides/auth.html).

Az alábbi példák bemutatják, hogyan lehet beállítani a C# és nyissa meg a fejléc:

```csharp
creds = ChannelCredentials.Create(baseCreds, CallCredentials.FromInterceptor(
                      async (context, metadata) =>
                      {
                          metadata.Add(new Metadata.Entry("authorization", "authKey"));
                          await Task.CompletedTask;
                      }));

```

```go
conn, err := grpc.Dial(serverAddr, 
    grpc.WithTransportCredentials(credentials.NewClientTLSFromCert(nil, "")),
    grpc.WithPerRPCCredentials(&authCreds{
    Key: "authKey"}))

type authCreds struct {
    Key string
}

func (c *authCreds) GetRequestMetadata(context.Context, uri ...string) (map[string]string, error) {
    return map[string]string{
        "authorization": c.Key,
    }, nil
}

func (c *authCreds) RequireTransportSecurity() bool {
    return true
}
```

### <a id="self-signed"></a>Szolgáltatások önaláírt tanúsítványokkal felhasználása

Ahhoz, hogy az ügyfél hitelesítése a kiszolgálóra egy önaláírt tanúsítvánnyal biztonságos két módja van:

* Az ügyfélrendszeren, állítsa be a `GRPC_DEFAULT_SSL_ROOTS_FILE_PATH` környezeti változót az ügyfélrendszeren a tanúsítvány-fájlra mutat.

* Hozhat létre, amikor egy `SslCredentials` objektumazonosító, a tanúsítvány-fájl tartalmának továbbítja a konstruktor.

Mindkét módszer használata azt eredményezi, a tanúsítványt használja, a legfelső szintű tanúsítvány gRPC.

> [!IMPORTANT]
> gRPC nem fogadja el a nem megbízható tanúsítványok. Nem megbízható tanúsítvánnyal meghiúsul egy `Unavailable` állapotkódot. A sikertelenség részleteinek tartalmazhat `Connection Failed`.
