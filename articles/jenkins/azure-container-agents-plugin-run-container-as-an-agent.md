---
title: "Az Azure-ban Jenkins és az Azure-tároló példányok a projekt létrehozása"
description: "Útmutató: az Azure-tároló ügynök Jenkins beépülő modul segítségével az Azure-ban Azure tároló példányok a projekt létrehozása"
author: tomarcher
manager: rloutlaw
ms.service: multiple
ms.workload: web
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: tarcher
ms.custom: Jenkins
ms.openlocfilehash: fc3ad4b68e29e9bd5666bb115306b452d074f682
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="build-a-project-in-azure-using-jenkins-and-azure-container-instances"></a>Az Azure-ban Jenkins és az Azure-tároló példányok a projekt létrehozása

Azure tároló-példányokon segítségével könnyen létrehozásához és futó virtuális gépeket, vagy egy magasabb szintű szolgáltatást elfogadása nélkül. Azure-tároló példányokon biztosít másodpercenként számlázási a kapacitásnak kell; így például build átmeneti munkaterhelések vonzó lehetőség.

Az alábbiak végrehajtásának módját ismerheti meg:
> [!div class="checklist"]
> * Kiszolgáló telepítése és konfigurálása a Jenkins az Azure-on
> * Telepítse és konfigurálja az Azure-tároló ügynökök beépülő modul a Jenkins
> * Az Azure-tároló példányok lehetővé teszi a [rugó PetClinic mintaalkalmazás](https://github.com/spring-projects/spring-petclinic)

## <a name="prerequisites"></a>Előfeltételek

- **Azure-előfizetés** – az Azure megvásárlási lehetőségeinek megismerése című [Azure megvásárlása](https://azure.microsoft.com/pricing/purchase-options/) vagy [ingyenes egy hónapos próbaverzió](https://azure.microsoft.com/pricing/free-trial/).

- **Az Azure CLI 2.0-s vagy Azure Cloud rendszerhéj** -telepítse a következő termékek, amelybe adja meg Azure parancsok egyikét:

    - [Az Azure CLI 2.0](/cli/azure/install-azure-cli?view=azure-cli-latest) -lehetővé teszi, hogy egy parancs vagy terminálablakot Azure parancsok futtatásához.
    - [Azure Cloud rendszerhéj](/azure/cloud-shell/quickstart.md) – webböngésző-alapú felhasználói élmény rendszerhéj. Felhő rendszerhéj lehetővé teszi a hozzáférést egy Azure felügyeleti feladatok szem előtt a beépített parancssori böngészőalapú felületén.

## <a name="install-a-jenkins-server-on-azure-using-the-jenkins-marketplace-image"></a>Azure-ban a Jenkins Piactéri lemezképhez Jenkins kiszolgáló telepítése

Jenkins támogatja, ahol a kiszolgáló delegáltak működik egy vagy több ügynököt projektek nagy számú futtatására, vagy adja meg a szükséges különböző környezetek egyetlen Jenkins telepítéséhez Jenkins összeállít egy modell, vagy teszteli. Ebben a szakaszban a lépések részletes útmutatót kiszolgáló telepítése és konfigurálása a Jenkins az Azure-on.

[!INCLUDE [jenkins-install-from-azure-marketplace-image](../../includes/jenkins-install-from-azure-marketplace-image.md)]

## <a name="connect-to-the-jenkins-server-running-on-azure"></a>Az Azure-on futó Jenkins kiszolgálóhoz

Miután Jenkins telepítette az Azure-on, Jenkins csatlakozni kell. A következő lépések végigvezetik Önt az Azure-on futó Jenkins VM SSH-kapcsolat beállítása. 

[!INCLUDE [jenkins-connect-to-jenkins-server-running-on-azure](../../includes/jenkins-connect-to-jenkins-server-running-on-azure.md)]

## <a name="update-jenkins-dns"></a>Update Jenkins DNS

Jenkins tudnia kell, a saját URL-címe, amikor azt vissza önmagára mutató hivatkozásokat hoz létre. Az URL-címe például kell használható, ha Jenkins közvetlen hivatkozásokat hozhat létre az eredményeket tartalmazó e-mailek küldése. 

Ez a szakasz végigvezeti a Jenkins URL-cím beállításakor.

1. A böngészőben navigáljon a következő Jenkins irányítópult `http://localhost:8080`.

1. Válassza ki **Jenkins kezelése**.

    ![Az Jenkins irányítópult Jenkins beállítások kezelése](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-manage-jenkins.png)

1. Válassza ki **rendszer konfigurálása**.

    ![A Jenkins irányítópult Jenkins beépülő modulok beállítás kezelése](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-configure-system.png)

1. A **Jenkins hely**, adja meg a Jenkins kiszolgáló URL-CÍMÉT.

1. Kattintson a **Mentés** gombra.

## <a name="update-jenkins-to-allow-java-network-launch-protocol-jnlp"></a>Frissítés Jenkins engedélyezéséhez Java hálózati indítása protokoll (JNLP)

A Jenkins ügynök kapcsolódik a Jenkins kiszolgálóhoz keresztül a Java hálózati indítása protokoll (JNLP). Ez a szakasz ismerteti a Jenkins kiszolgálóval való kommunikáció JNLP ügynökök port megadása.

1. Az Jenkins irányítópultján válassza **kezelése Jenkins**.

    ![Az Jenkins irányítópult Jenkins beállítások kezelése](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-manage-jenkins.png)

1. Válassza ki **globális biztonságának konfigurálása**.

    ![Az Jenkins irányítópult globális biztonságának konfigurálása](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-configure-global-security.png)

1. A **ügynökök**, jelölje be **rögzített**, és adja meg azt a portot. A képernyőfelvételen az 12345 portra érték példaként. Adja meg az adott környezetben legjobb port.

    ![Jenkins globális biztonsági beállítások lehetővé teszik a JNLP frissítése](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-set-jnlp.png)

1. Kattintson a **Mentés** gombra.

1. Azure CLI 2.0 vagy felhő rendszerhéj adja meg egy bejövő szabályt az Jenkins hálózati biztonsági csoport létrehozása a következő parancsot:

    ```azurecli
    az network nsg rule create  \
    --resource-group JenkinsResourceGroup \
    --nsg-name JenkinsNSG  \
    --name allow-https \
    --description "Allow access to port 12345 for HTTPS" \
    --access Allow \
    --protocol Tcp  \
    --direction Inbound \
    --priority 102 \
    --source-address-prefix "*"  \
    --source-port-range "*"  \
    --destination-address-prefix "*" \
    --destination-port-range "12345"
    ```

## <a name="create-and-add-an-azure-service-principal-to-the-jenkins-credentials"></a>Hozzon létre, és egy egyszerű Azure szolgáltatás hozzáadása a Jenkins hitelesítő adatok

Egy Azure szolgáltatás egyszerű Azure telepíteni kell. A következő lépések végigvezetik a folyamat létrehozása egy egyszerű szolgáltatásnév (Ha még nem rendelkezik), és a szolgáltatás egyszerű Jenkins frissítése.

1. Ha már rendelkezik egy egyszerű szolgáltatásnév (és tudja az előfizetés-azonosító, a bérlői, az appId és a jelszót), kihagyhatja ezt a lépést. Ha szeretne létrehozni egy rendszerbiztonsági tag, tekintse meg a cikk [hozzon létre egy Azure szolgáltatás egyszerű Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest). A rendszerbiztonsági tag létrehozásakor jegyezze fel az értékek az előfizetés-azonosító, a bérlői, az appId és a jelszót.

1. Válassza ki **hitelesítő adatok**.

    ![Hitelesítő adatok beállítása a Jenkins irányítópult kezelése](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-credentials.png)

1. A **hitelesítő adatok**, jelölje be **rendszer**.

    ![Hitelesítő adatok beállítást az Jenkins irányítópult kezelése](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-credentials-system.png)

1. Válassza ki **globális hitelesítő adatok (korlátlan)**.

    ![Globális hitelesítő adatok beállítást az Jenkins irányítópult kezelése](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-credentials-global.png)

1. Válassza ki **néhány hitelesítő adatokat vesz fel**.

    ![Adja hozzá a hitelesítő adatokat az Jenkins irányítópult](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-adding-credentials.png)

1. Az a **jellegű** legördülő listában válassza ki **Microsoft Azure szolgáltatás egyszerű** okozhat a lap egy egyszerű szolgáltatásnév hozzáadása adott mezők megjelenítéséhez. Ezt követően adja meg a kért értékeket a következőképpen:

    - **Hatókör** -válassza ki a kívánt beállítást **globális (Jenkins csomópontok, elemeket, az összes alárendelt elemek, stb.)** .
    - **Előfizetés-azonosító** -futtatásakor megadott Azure-előfizetés-azonosító használata `az account set`.
    - **Ügyfél-azonosító** -használatát a `appId` által visszaadott érték `az ad sp create-for-rbac`.
    - **Titkos Ügyfélkulcsot** -használatát a `password` által visszaadott érték `az ad sp create-for-rbac`.
    - **A bérlői azonosító** -használatát a `tenant` által visszaadott érték `az ad sp create-for-rbac`.
    - **Azure-alapú környezetben** – Itt adhatja meg `Azure`.
    - **Azonosító** -meg `myTestSp`. Ezt az értéket az oktatóanyag később használja.
    - **Leírás** – (nem kötelező) adja meg a rendszerbiztonsági tag leírás értékét.

    ![Adja meg az új egyszerű szolgáltatástulajdonságok az Jenkins irányítópult](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-new-principal-properties.png)

1. Miután megadta az információt a rendszerbiztonsági tag, is választhat **ellenőrizze a szolgáltatás egyszerű** annak érdekében, hogy minden helyesen működik. Ha az egyszerű szolgáltatás helyesen van definiálva, megjelenik egy üzenet, "sikeresen ellenőrizte a Microsoft Azure szolgáltatás egyszerű." alább a **leírás** mező.

1. Ha elkészült, válassza ki a **OK** a rendszerbiztonsági tag hozzáadása Jenkins. A Jenkins irányítópult megjeleníti az újonnan hozzáadott rendszerbiztonsági tag a **globális hitelesítő adatok** lap.

## <a name="create-an-azure-resource-group-for-your-azure-container-instances"></a>Az Azure erőforrás-csoport létrehozása az Azure-tároló példányok

Az Azure tároló példányok kell helyezni egy Azure-erőforráscsoportot. Egy Azure erőforráscsoport egy olyan tároló, amely egy Azure megoldás kapcsolódó erőforrásokat tárol.

Azure CLI 2.0 vagy felhő rendszerhéj, adja meg a következő parancsot egy nevű erőforráscsoport létrehozásához `JenkinsAciResourceGroup` helyen `eastus`:

```azurecli
az group create --name JenkinsAciResourceGroup --location eastus
```

Ha elkészült, a `az group create` parancs az alábbi példához hasonló eredményeit jeleníti meg:

```JSON
{
  "id": "/subscriptions/<subscriptionId>/resourceGroups/JenkinsAciResourceGroup",
  "location": "eastus",
  "managedBy": null,
  "name": "JenkinsAciResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="install-the-azure-container-agents-plugin-for-jenkins"></a>Az Azure-tároló ügynökök beépülő modul telepítése Jenkins

Ha már telepítette az Azure-tároló ügynökök beépülő modul, ez a szakasz kihagyhatja.

Miután az Azure erőforráscsoport létrehozásánál a Jenkins ügynök, a következő lépések bemutatják az Azure-tároló ügynökök beépülő modul telepítése:

1. Az Jenkins irányítópultján válassza **kezelése Jenkins**.

    ![Az Jenkins irányítópult Jenkins beállítások kezelése](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-manage-jenkins.png)

1. Válassza ki **beépülő modulok kezelése**.

    ![A Jenkins irányítópult Jenkins beépülő modulok beállítás kezelése](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-manage-plugins.png)

1. Válassza ki **elérhető**.

    ![Elérhető Jenkins beépülő modulok lehetőség az Jenkins irányítópult megtekintése](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-view-available-plugins.png)

1. Adja meg `Azure Container Agents` azokat a **szűrő** szövegmezőben. (A lista szűrők, jelennek meg a szöveget.)

    ![A Jenkins telepített beépülő modulok az Jenkins irányítópult szűrése](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-filter-available-plugins.png)

1. Jelölje be a jelölőnégyzetet a a **Azure tároló ügynökök** beépülő modul, és a telepítési lehetőségek közül. Ez a bemutató célokra I kijelölt a **újraindítása nélküli** lehetőséget.

    ![A Jenkins irányítópultról az Azure-tároló ügynökök beépülő modulok telepítése](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-install-aks-agent-plugin.png)

1.  A telepíteni kívánt plugin(s) beállítás kiválasztása után a Jenkins irányítópult egy oldal, és részletesen leírja, milyen telepítése állapotát jeleníti meg.

    ![Az Azure-tároló ügynökök beépülő modulok telepítése a Jenkins irányítópultról telepítési állapotát.](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-install-aks-agent-plugin-confirmation.png)

    Vissza a Jenkins irányítópult fő lapján, válassza ki a **vissza az első lapra**.

## <a name="configure-the-azure-container-agents-plugin"></a>Az Azure-tároló ügynökök beépülő modul konfigurálása

Az Azure-tároló ügynökök beépülő modul telepítése után ez a szakasz végigvezeti Önt a beépülő modul a Jenkins irányítópult belül.

1. Az Jenkins irányítópultján válassza **kezelése Jenkins**.

    ![Az Jenkins irányítópult Jenkins beállítások kezelése](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-manage-jenkins.png)

1. Válassza ki **rendszer konfigurálása**.

    ![A Jenkins irányítópult Jenkins beépülő modulok beállítás kezelése](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-configure-system.png)

1. Keresse meg a **felhő** az oldal, illetve a szakasz a lap alján a **hozzáadása egy új felhőalapú** legördülő listában válassza ki **Azure tároló példány**.

    ![Új felhőalapú szolgáltató hozzáadása a Jenkins irányítópultról](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-new-cloud-provider.png)

1. Az a **Azure tároló példány** területen adja meg a következő értékeket:

    - **A felhő neve** – (nem kötelező, mint az alapértelmezett érték egy automatikusan létrehozott nevet.) Adja meg, hogy ez a példány nevét. 
    - **Az Azure hitelesítő** – válassza ki a legördülő nyílra, majd válassza ki a `myTestSp` bejegyzést, amely azonosítja az Azure szolgáltatás egyszerű korábban létrehozott.
    - **Erőforráscsoport** – válassza ki a legördülő nyílra, majd válassza ki a `JenkinsAciResourceGroup` bejegyzést, amely azonosítja az Azure-tároló példány erőforráscsoport korábban létrehozott.

    ![Az Azure-tároló példány tulajdonságainak meghatározása](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-aci-properties.png)

1. Válassza ki a **tároló sablon hozzáadása** legördülő nyílra, majd válassza ki **Aci tároló sablon**.

1. Az a **Aci tároló sablon** területen adja meg a következő értékeket:

    - **Név** -meg `ACI-container`.
    - **Címkék** -meg `ACI-container`.
    - **Docker kép** -adja meg `cloudbees/jnlp-slave-with-java-build-tools`

    ![Az Azure-tároló példány lemezkép tulajdonságainak meghatározása](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-aci-image-properties.png)

1. Válassza ki **speciális**.

1. Válassza ki a **megőrzési stratégia** legördülő nyílra, és válassza ki **tároló üresjárati megőrzési stratégia**. Ez a beállítás kiválasztásával Jenkins az ügynök tartja, amíg nincs új feladat végrehajtása az ügynök és a megadott tétlenségi idő eltelte.

    ![A tároló példány Azure megőrzési stratégia meghatározása](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-aci-retention-strategy.png)

1. Kattintson a **Mentés** gombra.

## <a name="create-the-spring-petclinic-application-job-in-jenkins"></a>A rugó PetClinic alkalmazás feladat létrehozása a Jenkins

A következő lépések végigvezetik Jenkins feladat - freestyle projektként - hozható létre a rugó PetClinic-alkalmazás létrehozása.

1. Az Jenkins irányítópultján válassza **új elem**.

    ![Új elem menüpont az Jenkins irányítópult](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-new-item.png)

1. Adja meg `myPetClinicProject` elem nevét, és válassza ki a **Freestyle projekt**.

    ![Új freestyle projektet az Jenkins irányítópult](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-new-freestyle-project.png)

1. Kattintson az **OK** gombra.

1. Válassza ki a **általános** lapot, és adja meg a következő értékeket:

    - **Korlátozása, ahol a projekt futtatható** -ezt a beállítást.
    - **Címke kifejezés** -meg `ACI-container`. Ha kilép a mezőt, egy üzenet jelenik meg, erősítse meg, hogy a címke a felhő konfigurációja az előző lépésben létrehozott által kiszolgált.

    ![Általános beállítások megadása a Jenkins irányítópulton freestyle új projekt](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-new-freestyle-project-general.png)

1. Válassza ki a **forrás kód felügyeleti** lapot, és adja meg a következő értékeket:

    - **Forrás-kód felügyeleti** – Itt adhatja meg **Git**.
    - **Tárház URL-CÍMÉT** -adja meg a következő URL-címet a rugó PetClinic minta alkalmazás GitHub-tárház: `https://github.com/spring-projects/spring-petclinic.git`.

1. Válassza ki a **Build** lapot, és hajtsa végre a következő feladatokat:

    - Válassza ki a **Hozzáadás összeállítása lépés** legördülő nyílra, és válassza ki **meghívása a legfelső szintű Maven célok**.

    - A **célok**, adja meg `package`.

1. Válassza ki **mentése** menteni az új projekt definícióját.

## <a name="build-the-spring-petclinic-application-job-in-jenkins"></a>A rugó PetClinic alkalmazás Jenkins feladat létrehozása

A projekt építi! Ez a szakasz ismerteti, hogyan hozhat létre a projektet az Jenkins irányítópulton.

1. Az Jenkins irányítópultján válassza `myPetClinicProject`.

    ![Válassza ki a projekt a Jenkins irányítópult felépíteni.](./media/azure-container-agents-plugin-run-container-as-an-agent/select-project-to-build.png)

1. Válassza ki **Létrehozás most**. 

    ![A Jenkins irányítópultról a projekt felépítéséhez.](./media/azure-container-agents-plugin-run-container-as-an-agent/build-project.png)

1. Build a Jenkins indításakor a build várólistára van állítva. Egy Azure-tároló ügynöknek a összeállítása nem lehet futtatni mindaddig, amíg a Azure-tároló ügynök elindult és online állapotba. Addig megjelenik egy üzenet, jelezve, az ügynök neve, és arra, hogy a build függőben. (Ez a folyamat körülbelül öt percet vesz igénybe, de csak akkor szükség az első alkalommal használja az ügynök build. Későbbi verziókat sokkal gyorsabb, mint az ügynök ezen a ponton nem online.)

    ![A build várólistára van állítva, amíg az ügynök hozzák létre, és online állapotba.](./media/azure-container-agents-plugin-run-container-as-an-agent/build-pending.png)

    A build elindul, ha egy barber sarkpontot folyamatjelző jelzi, hogy fut-e a létrehozás:

    ![A létrehozás után megjelenik a barber sarkpontot folyamatjelző fut.](./media/azure-container-agents-plugin-run-container-as-an-agent/build-running.png)

1. Amikor a barber sarkpontot folyamatjelző sáv eltűnik, válassza ki a buildszám melletti nyílra. Válassza ki a helyi menüből **a konzol kimeneti**.

    ![Kattintson a konzol napló menüpont a build információk megtekintéséhez.](./media/azure-container-agents-plugin-run-container-as-an-agent/build-console-log-menu.png)

1. A konzol naplózási adatok kibocsátott a létrehozási folyamat során. (Az Azure-ügynököt távolról végrehajtása a build létrehozott adatai, például) build adatok megtekintéséhez válassza ki a **teljes napló**.

    ![A teljes naplót hivatkozásra build további részletes információk megtekintése.](./media/azure-container-agents-plugin-run-container-as-an-agent/build-console-log.png)

    A teljes naplót build részletesebb információkat jelenít meg:

    ![A teljes naplót build részletesebb információkat jelenít meg.](./media/azure-container-agents-plugin-run-container-as-an-agent/build-console-log-full.png)
    
1. A build törlése megtekintéséhez a napló alján görgessen.

    ![A build törlése a build napló alján jeleníti meg.](./media/azure-container-agents-plugin-run-container-as-an-agent/build-disposition.png)

## <a name="clean-up-azure-resources"></a>Azure-erőforrások törlése

Ebben az oktatóanyagban létre két Azure erőforráscsoport-sablonok lévő erőforrások: 
    - `JenkinsResourceGroup` -A Jenkins kiszolgáló Azure-erőforrást tartalmaz.
    - `JenkinsAciResourceGroup` -A Jenkins ügynök az Azure-erőforrásokat tartalmazza.
    
Ha már nem szeretné használni, az erőforrásokat egy Azure erőforráscsoport, az erőforrás csoport használatával törölheti a `az group delete` parancsot a következőképpen (cseréje a &lt;resourceGroup > helyőrzőt a kívánt erőforrás-csoport neve Törlés):

```azurecli
az group delete -n <resourceGroup>
```

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Látogasson el a Jenkins Azure központ, a legújabb cikkeket, és minták](https://docs.microsoft.com/azure/jenkins/)