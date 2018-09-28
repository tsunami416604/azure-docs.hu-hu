---
title: API-verzióprofilok használata Java az Azure Stackben |} A Microsoft Docs
description: Ismerje meg az Azure Stackben Java API-verzióprofilok használatával.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2018
ms.author: sethm
ms.reviewer: sijuman
ms.openlocfilehash: b5a876ea8b5cc70ee0ca0dcac8628c12dc2b009b
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47413990"
---
# <a name="use-api-version-profiles-with-java-in-azure-stack"></a>API-verzióprofilok használata az Azure Stackben Javával

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek és az Azure Stack fejlesztői készlete*

A Java SDK az Azure Stack Resource Manager biztosít eszközöket és az infrastruktúra kezelését. Az SDK-t az erőforrás-szolgáltatók közé tartozik a számítási, hálózatkezelési, tárolási, alkalmazásszolgáltatások, és [KeyVault](../../key-vault/key-vault-whatis.md). A Java SDK API-profilok szerint, beleértve a függőségeket a Pom.xml fájlt, amely betölti a .java fájl a megfelelő modulokat magában foglalja. Azonban hozzáadhat több profilt függőségei, mint például a **2018-03-01-hibrid**, vagy **legújabb** , az Azure-profilra. A függőségek használatával tölti be a helyes modulját, hogy az erőforrás-típus létrehozásakor kijelölheti a API-verziót a használni kívánt egyes profilokhoz. Ez lehetővé teszi, hogy a legújabb verziókat használhatja az Azure-ban, a legújabb API-verzióit ellen az Azure Stack fejlesztése során. Egy igazi hibrid felhőalapú fejlesztői felületet biztosít a Java SDK használata lehetővé teszi. A Java SDK API-profilok engedélyezése a hibrid felhőalapú fejlesztés segít a globális Azure-erőforrások és az Azure Stackben erőforrások közötti váltáshoz.

## <a name="java-and-api-version-profiles"></a>Java- és API-verzióprofilok

Egy API-profil az erőforrás-szolgáltatók és API-verziók. Az API-profilok segítségével egy erőforrás-szolgáltatói csomag az egyes erőforrástípusok legújabb és legnagyobb stabil verziójának beszerzéséhez.

- Az összes szolgáltatást a legújabb verziókat használhatja a **legújabb** profil, a függőség.
    
   - A legújabb profil használatára, a függőség van **következőt: com.microsoft.azure**.

   - Az Azure Stack kompatibilis szolgáltatások használatához a **com.microsoft.azure.profile\_2018\_03\_01\_hibrid** profilt.
    
      - Ez a adható meg a Pom.xml fájlt egy függőséget alkot, ami automatikusan betölti a modulokat, ha a megfelelő osztályt a legördülő listából válassza el, mint a .NET használatával.
        
          - Minden modul tetején a következőképpen jelenik meg:         
           `Import com.microsoft.azure.management.resources.v2018_03_01.ResourceGroup`
             

  - Függőségek a következőképpen jelenik meg:
     ```xml
     <dependency>
     <groupId>com.microsoft.azure.profile_2018_03_01_hybrid</groupId>
     <artifactId>azure</artifactId>
     <version>1.0.0-beta</version>
     </dependency>
     ```

  - Adott API-verziók az erőforrástípushoz az adott erőforrás-szolgáltatón keresztül intellisense definiált adott API-verziók használatához.

Vegye figyelembe, hogy egyesítheti az összes beállítás ugyanabban az alkalmazásban.

## <a name="install-the-azure-java-sdk"></a>Telepítse az Azure Java SDK

Az alábbi lépések segítségével a Java SDK telepítése:

1.  Kövesse a Git telepítése a hivatalos utasításokat. Útmutatásért lásd: [első lépések – Git telepítése](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).

2.  Hivatalos utasításokat követve telepítse a [Java SDK](http://zulu.org/download/)) és [Maven](https://maven.apache.org/). A megfelelő verzió legyen az Java Developer Kit 8 verzióját. A megfelelő Apache Maven a 3.0-s verzió vagy újabb. A JAVA_HOME környezeti változót a rövid útmutató a Java Development Kitet telepítési helyére kell beállítani. További információkért lásd: [az első függvény létrehozása a Java és Maven](../../azure-functions/functions-create-first-java-maven.md).

3.  A megfelelő függőségi csomagokat telepíteni, nyissa meg a Pom.xml fájlt a Java-alkalmazás. Adja hozzá a függőség, az alábbi kódban látható módon:

   ```xml  
   <dependency>
   <groupId>com.microsoft.azure.profile_2018_03_01_hybrid</groupId>
   <artifactId>azure</artifactId>
   <version>1.0.0-beta</version>
   </dependency>
   ```

4.  A csomagokat, amelyeket telepíteni kell a használni kívánt profilt verziójától függ. A csomag nevét, a profil-verziók a következők:
    
   - **com.microsoft.Azure.profile\_2018\_03\_01\_hibrid**
   - **következőt: com.microsoft.Azure**
      - **legfrissebb**

5.  Ha nem érhető el, hozzon létre egy előfizetést és az előfizetés-azonosító későbbi használatra. Előfizetés létrehozásával kapcsolatos útmutatásért lásd: [ajánlatok, előfizetések létrehozása az Azure Stackben](../azure-stack-subscribe-plan-provision-vm.md).

6.  Hozzon létre egy egyszerű szolgáltatást, és mentse az ügyfél-Azonosítót és a titkos Ügyfélkulcsot. Az Azure stack-beli szolgáltatásnév létrehozása az utasításokért lásd: [alkalmazások elérést biztosíthat az Azure Stack](../azure-stack-create-service-principals.md). Vegye figyelembe, hogy az ügyfél-azonosító néven is ismert az Alkalmazásazonosítót egyszerű szolgáltatás létrehozásakor.

7.  Ellenőrizze, hogy az egyszerű szolgáltatást a közreműködői és tulajdonosi szerepkör-előfizetésében. Szerepkör hozzárendelése egyszerű szolgáltatást, lásd: [alkalmazások elérést biztosíthat az Azure Stack](../azure-stack-create-service-principals.md).

## <a name="prerequisites"></a>Előfeltételek

Azure .NET SDK használata az Azure Stack használatával, adja meg a következő értékeket, és adja meg az értékeket a környezeti változókat. A környezeti változók beállítása, tekintse meg az alábbi táblázat az operációs rendszerének utasításokat.

| Érték                     | Környezeti változók | Leírás                                                                                                                                                                                                          |
| ------------------------- | --------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Bérlőazonosító                 | TENANT_ID            | Az Azure Stack értékét [ <span class="underline">bérlőazonosító</span>](../azure-stack-identity-overview.md).                                                          |
| Ügyfél-azonosító                 | CLIENT_ID             | A szolgáltatás egyszerű alkalmazást azonosító mentése az előző szakaszban Ez a dokumentum az egyszerű szolgáltatás létrehozásakor.                                                                                              |
| Előfizetés azonosítója           | SUBSCRIPTION_ID      | A [ <span class="underline">előfizetés-azonosító</span> ](../azure-stack-plan-offer-quota-overview.md#subscriptions) van, hogy miként férhetnek hozzá az ajánlatok az Azure Stackben.                |
| Titkos ügyfélkulcs             | TITKOS ÜGYFÉLKÓDOT        | A szolgáltatás egyszerű alkalmazás titkos kulcs mentése egyszerű szolgáltatás létrehozásakor.                                                                                                                                   |
| Resource Manager-végpont | VÉGPONT              | Lásd: [ <span class="underline">az Azure Stack resource manager-végpont</span>](../user/azure-stack-version-profiles-ruby.md#the-azure-stack-resource-manager-endpoint). |
| Hely                  | RESOURCE_LOCATION    | Helyi az Azure Stackhez                                                                                                                                                                                                |

Az Azure stack a bérlő Azonosítójának megkereséséhez kövesse az utasításokat található [Itt](../azure-stack-csp-ref-operations.md). A környezeti változók beállítása, tegye a következőket:

### <a name="microsoft-windows"></a>A Microsoft Windows

A környezeti változók beállítása egy Windows parancssorban, használja a következő formátumot:

```shell
Set Azure_Tenant_ID=<Your_Tenant_ID>
```

### <a name="macos-linux-and-unix-based-systems"></a>macOS, Linux és Unix-alapú rendszerek

Unix-alapú rendszerekben használhatja a következő parancsot:

```shell
Export Azure_Tenant_ID=<Your_Tenant_ID>
```

### <a name="the-azure-stack-resource-manager-endpoint"></a>Az Azure Stack resource manager-végpont

A Microsoft Azure Resource Manager-keretrendszert, amely lehetővé teszi a rendszergazdák üzembe helyezése, kezelése és monitorozása az Azure-erőforrások számára. Az Azure Resource Manager képes kezelni ezeket a feladatokat, csoportként, nem pedig külön-külön, egyetlen művelettel.

A metaadat-információkat is kérhet a Resource Manager-végpontot. A végpont egy JSON-fájlt a kód futtatásához szükséges adatokat adja vissza.

Vegye figyelembe az alábbiakat:

- A **ResourceManagerUrl** van az Azure Stack Development Kit (ASDK): https://management.local.azurestack.external/

- A **ResourceManagerUrl** integrált rendszerek van: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/` a szükséges metaadatok lekérése céljából: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`

JSON-mintafájlt:

```json
{ 
   "galleryEndpoint": "https://portal.local.azurestack.external:30015/",
   "graphEndpoint": "https://graph.windows.net/",
   "portal Endpoint": "https://portal.local.azurestack.external/",
   "authentication": 
      {
      "loginEndpoint": "https://login.windows.net/",
      "audiences": ["https://management.<yourtenant>.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
      }
}
```

## <a name="existing-api-profiles"></a>Meglévő API-profilok

1.  **com.microsoft.Azure.profile\_2018\_03\_01\_hibrid**: az Azure Stackhez készült legújabb profil. Ez a profil Services kompatibilis az Azure Stack mindaddig, amíg a 1808 blokk vagy a további használhatják.

2.  **com.microsoft.Azure.profile\_2017\_03\_09\_profil**: Ha egy alacsonyabb, mint a 1808 build stamp, használja ezt a profilt.

3.  **következőt: com.microsoft.Azure**: az összes szolgáltatást a legújabb verziókat álló profilt. Az összes szolgáltatást a legújabb verziókat használhatja.

Az Azure Stacket és API-profilokkal kapcsolatos további információkért lásd: a [összegzése az API-profilok](../user/azure-stack-version-profiles.md#summary-of-api-profiles).

## <a name="azure-java-sdk-api-profile-usage"></a>Azure Java SDK API profil használata

Az alábbi kód hitelesíti az Azure Stacken a szolgáltatásnevet. A rendszer létrehoz egy jogkivonatot a bérlő által Azonosítóját és hitelesítési alap, amelyet kifejezetten az Azure Stack:

```java
AzureTokenCredentials credentials = new ApplicationTokenCredentials(client, tenant, key, AZURE_STACK)
                    .withDefaultSubscriptionId(subscriptionId);
            Azure azureStack = Azure.configure().withLogLevel(com.microsoft.rest.LogLevel.BASIC)
                    .authenticate(credentials, credentials.defaultSubscriptionId());
```

Ez lehetővé teszi, hogy az API-profiltól függ az alkalmazás telepítése sikeresen megtörtént az Azure Stack.

## <a name="define-azure-stack-environment-setting-functions"></a>Azure Stack-környezet beállítás függvények definiálása

Az Azure Stack-felhő a megfelelő végpontokkal rendelkező regisztrálásához használja a következő kódot:

```java
AzureEnvironment AZURE_STACK = new AzureEnvironment(new HashMap<String, String>() {
                {
                    put("managementEndpointUrl", settings.get("audience"));
                    put("resourceManagerEndpointUrl", armEndpoint);
                    put("galleryEndpointUrl", settings.get("galleryEndpoint"));
                    put("activeDirectoryEndpointUrl", settings.get("login_endpoint"));
                    put("activeDirectoryResourceId", settings.get("audience"));
                    put("activeDirectoryGraphResourceId", settings.get("graphEndpoint"));
                    put("storageEndpointSuffix", armEndpoint.substring(armEndpoint.indexOf('.')));
                    put("keyVaultDnsSuffix", ".adminvault" + armEndpoint.substring(armEndpoint.indexOf('.')));
                }
            });
```

A `getActiveDirectorySettings` hívást az alábbi kódot a végpontok lekéri a metaadatokat végpontok. Meghatározza a végrehajtott hívás környezeti változókat:

```java
public static HashMap<String, String>
getActiveDirectorySettings(String armEndpoint) {

HashMap<String, String> adSettings = new HashMap<String, String>();

try {

// create HTTP Client
HttpClient httpClient = HttpClientBuilder.create().build();

// Create new getRequest with below mentioned URL
HttpGet getRequest = new
HttpGet(String.format("%s/metadata/endpoints?api-version=1.0",
armEndpoint));

// Add additional header to getRequest which accepts application/xml
data
getRequest.addHeader("accept", "application/xml");

// Execute request and catch response
HttpResponse response = httpClient.execute(getRequest);
```

## <a name="samples-using-api-profiles"></a>API-profilok használatával minták

Az alábbi GitHub-minták hivatkozásként használható megoldások létrehozása a .NET-keretrendszer és az Azure Stack API profilok:

  - [Erőforráscsoportok kezelése](https://github.com/viananth/resources-java-manage-resource-group/tree/stack/Hybrid)

  - [Storage-fiókok kezelése](https://github.com/viananth/storage-java-manage-storage-accounts/tree/stack/Hybrid)

  - [Virtuális gép kezelése](https://github.com/viananth/compute-java-manage-vm/tree/stack/Hybrid)

### <a name="sample-unit-test-project"></a>Minta egység tesztelő projektet 

1.  Klónozza a tárházat, a következő paranccsal:
    
    `git clone https://github.com/viananth/resources-java-manage-resource-group/tree/stack/Hybrid`

2.  Azure-beli szolgáltatásnév létrehozása, és rendelje hozzá egy szerepkörhöz az előfizetés eléréséhez. Egyszerű szolgáltatás létrehozásával kapcsolatos útmutatóért lásd: [tanúsítvánnyal egyszerű szolgáltatás létrehozása az Azure PowerShell használatával](../azure-stack-create-service-principals.md).

3.  A következő szükséges értékek beolvasása:
    
   1.  Bérlőazonosító
   2.  Ügyfél-azonosító
   3.  Titkos ügyfélkulcs
   4.  Előfizetés azonosítója
   5.  Resource Manager-végpont
   6.  Erőforrás helye

4.  Állítsa be az alábbi környezeti változókat a Szolgáltatásnévből létrehozott a parancssor használatával lekért információk segítségével:
    
   1. Exportálás TENANT_ID = {a bérlő azonosítóját}
   2. Exportálás CLIENT_ID = {az ügyfél-azonosító}
   3. exportálja a titkos ügyfélkódot = {az Ügyfélkód}
   4. Exportálás SUBSCRIPTION_ID = {az előfizetés azonosítója}
   5. Exportálás ARM_ENDPOINT = {az Azure Stack Resource manager URL-cím}
   6. Exportálás RESOURCE_LOCATION = {location helyezhető Azure stacket}

   A Windows, használja **beállítása** helyett **exportálása**.

5.  Használja a `getactivedirectorysettings` kód lekérése az arm-metaadatok végpontja, és a HTTP-ügyfelének használatával állítsa be a végpont adatait.

   ```java
   public static HashMap<String, String> getActiveDirectorySettings(String armEndpoint) {
   HashMap<String, String> adSettings = new HashMap<String,> String>();

   try {

   // create HTTP Client
   HttpClient httpClient = HttpClientBuilder.create().build();

   // Create new getRequest with below mentioned URL
   HttpGet getRequest = new
   HttpGet(String.format("%s/metadata/endpoints?api-version=1.0", armEndpoint));

   // Add additional header to getRequest which accepts application/xml data
   getRequest.addHeader("accept", "application/xml");

   // Execute request and catch response
   HttpResponse response = httpClient.execute(getRequest);
   ```

7.  Adja hozzá a 2018-03-01-hibrid profil használata az Azure Stack a következő függőséget a pom.xml fájlt. Ezt a függőséget telepíti a modulokat a profilhoz tartozó számítási, hálózatkezelési, tárolási, KeyVault és App Services erőforrás-szolgáltatókat.
    
   Vegye figyelembe, hogy a cél Azure legfrissebb profilt is használhatja:
        
   ```xml
   <dependency>
   <groupId>com.microsoft.azure.profile_2018_03_01_hybrid</groupId>
   <artifactId>azure</artifactId>
   <version>1.0.0-beta</version>
   </dependency>
   ```

8.  Meg volt nyitva, a környezeti változókat a parancssort írja be a következő sort:
    
   ```shell
   mvn clean compile exec:java
   ```

## <a name="next-steps"></a>További lépések

API-profilokkal kapcsolatos további információkért lásd:

- [Az Azure Stackben API-verzióprofilok kezelése](azure-stack-version-profiles.md)
- [Erőforrás-szolgáltató API-ja verziókat támogatja profilok](azure-stack-profiles-azure-resource-manager-versions.md)
