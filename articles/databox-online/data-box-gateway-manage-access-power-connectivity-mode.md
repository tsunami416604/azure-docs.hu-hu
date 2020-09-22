---
title: Eszköz-hozzáférés, energiaellátás és kapcsolódási mód Azure Data Box Gateway
description: Útmutató az adatok Azure-ba való átvitelét segítő Azure Data Box Gateway eszköz hozzáférésének, teljesítményének és csatlakozási módjának kezeléséhez
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: how-to
ms.date: 06/03/2019
ms.author: alkohli
ms.openlocfilehash: 1035b0afee9821020673acbc813b31cba3e2fd90
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90893942"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-data-box-gateway"></a>A Azure Data Box Gateway hozzáférésének, teljesítményének és kapcsolati módjának kezelése

Ez a cikk azt ismerteti, hogyan kezelhető a Azure Data Box Gateway hozzáférési, energiagazdálkodási és kapcsolódási módja. Ezek a műveletek a helyi webes felületen vagy a Azure Portalon keresztül hajthatók végre. 

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
>
> * Eszközök hozzáférésének kezelése
> * Kapcsolati mód kezelése
> * A Power kezelése

## <a name="manage-device-access"></a>Eszközök hozzáférésének kezelése

A Data Box Gateway eszköz hozzáférését az eszköz jelszavának használatával szabályozhatja. A jelszó a helyi webes felületen keresztül módosítható. Alaphelyzetbe állíthatja az eszköz jelszavát is a Azure Portalban.

### <a name="change-device-password"></a>Az eszköz jelszavának módosítása

Az eszköz jelszavának módosításához kövesse az alábbi lépéseket a helyi felhasználói felületen.

1. A helyi webes KEZELŐFELÜLETen lépjen a **karbantartás > jelszavának módosítása**elemre.
2. Adja meg az aktuális jelszót, majd az új jelszót. A megadott jelszónak 8 és 16 karakter közöttinek kell lennie. A jelszónak legalább hármat tartalmaznia kell a következő karakterek közül: nagybetűk, kisbetűk, számok és speciális karakterek. Erősítse meg az új jelszót.

    ![Change password](media/data-box-gateway-manage-access-power-connectivity-mode/change-password-1.png)

3. Kattintson a **jelszó módosítása**gombra.
 
### <a name="reset-device-password"></a>Eszköz jelszavának alaphelyzetbe állítása

A munkafolyamat alaphelyzetbe állítása nem igényli, hogy a felhasználó felidézze a régi jelszót, és akkor is hasznos, ha a jelszó elvész. Ezt a munkafolyamatot a Azure Portal hajtja végre.

1. A Azure Portal lépjen az **áttekintés > rendszergazdai jelszó visszaállítása**elemre.

    ![Új jelszó létrehozása](media/data-box-gateway-manage-access-power-connectivity-mode/reset-password-1.png)

 
2. Adja meg az új jelszót, majd erősítse meg. A megadott jelszónak 8 és 16 karakter közöttinek kell lennie. A jelszónak legalább hármat tartalmaznia kell a következő karakterek közül: nagybetűk, kisbetűk, számok és speciális karakterek. Kattintson az **Alaphelyzetbe állítás**gombra.

    ![Új jelszó létrehozása](media/data-box-gateway-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-resource-access"></a>Erőforrás-hozzáférés kezelése

A Azure Stack Edge Pro/Data Box Gateway, IoT Hub és Azure Storage-erőforrás létrehozásához jogosultságokat kell biztosítania közreműködőként vagy magasabb szintű erőforrás-csoport szintjén. Szükség van a megfelelő erőforrás-szolgáltatók regisztrálására is. Az aktiválási kulcsot és a hitelesítő adatokat is tartalmazó műveletek esetében a Azure Active Directory Graph APIra vonatkozó engedélyek is szükségesek. Ezeket a következő szakaszokban ismertetjük.

### <a name="manage-microsoft-graph-api-permissions"></a>Microsoft Graph API-engedélyek kezelése

Az Azure Stack Edge Pro-eszköz aktiválási kulcsának létrehozásakor, illetve a hitelesítő adatokat igénylő műveletek elvégzéséhez engedélyekkel kell rendelkeznie Microsoft Graph API-hoz. A hitelesítő adatokat igénylő műveletek a következők lehetnek:

-  Megosztás létrehozása társított Storage-fiókkal.
-  Hozzon létre egy felhasználót, aki hozzáfér az eszközön található megosztásokhoz.

Ahhoz, hogy a rendszer `User` képes legyen hozzáférni Active Directory bérlőhöz `Read all directory objects` . Nem lehet vendég felhasználó, mert nem rendelkezik engedéllyel a szolgáltatáshoz `Read all directory objects` . Ha vendég, akkor az olyan műveleteket, mint például az aktiválási kulcs létrehozása, a megosztás létrehozása a Azure Stack Edge Pro-eszközön, a felhasználó létrehozása sikertelen lesz.

A felhasználók Microsoft Graph API-hoz való hozzáférésének biztosításával kapcsolatos további információkért lásd: [Microsoft Graph engedélyek referenciája](https://docs.microsoft.com/graph/permissions-reference).

### <a name="register-resource-providers"></a>Erőforrás-szolgáltatók regisztrálása

Ha erőforrást szeretne kiépíteni az Azure-ban (a Azure Resource Manager modellben), szüksége lesz egy erőforrás-szolgáltatóra, amely támogatja az adott erőforrás létrehozását. Ha például egy virtuális gépet szeretne kiépíteni, az előfizetésben elérhető "Microsoft. számítási" erőforrás-szolgáltatónak kell lennie.
 
Az erőforrás-szolgáltatók az előfizetés-szinten vannak regisztrálva. Alapértelmezés szerint minden új Azure-előfizetés előre regisztrálva valamelyik gyakran használt erőforrás-szolgáltatónál. A "Microsoft. DataBoxEdge" erőforrás-szolgáltatója nem szerepel a listán.

Nem kell hozzáférési engedélyeket megadnia az előfizetési szinthez ahhoz, hogy a felhasználók olyan erőforrásokat hozzanak létre, mint például a "Microsoft. DataBoxEdge" azon erőforráscsoportok, amelyeken a tulajdonos jogosultságai vannak, feltéve, hogy az erőforrás-szolgáltatók már regisztrálva vannak.

Mielőtt megpróbál létrehozni egy erőforrást, győződjön meg arról, hogy az erőforrás-szolgáltató regisztrálva van az előfizetésben. Ha az erőforrás-szolgáltató nincs regisztrálva, meg kell győződnie arról, hogy az új erőforrást létrehozó felhasználónak elegendő jogosultsága van ahhoz, hogy regisztrálja a szükséges erőforrás-szolgáltatót az előfizetési szinten. Ha még nem tette meg, akkor a következő hibaüzenet jelenik meg:

*Az előfizetés \<Subscription name> nem rendelkezik engedéllyel az erőforrás-szolgáltató (k) regisztrálásához: Microsoft. DataBoxEdge.*


A jelenlegi előfizetésben regisztrált erőforrás-szolgáltatók listájának lekéréséhez futtassa a következő parancsot:

```PowerShell
Get-AzResourceProvider -ListAvailable |where {$_.Registrationstate -eq "Registered"}
```

Azure Stack Edge Pro-eszköz esetében `Microsoft.DataBoxEdge` regisztrálni kell. A regisztráláshoz `Microsoft.DataBoxEdge` az előfizetés rendszergazdájának a következő parancsot kell futtatnia:

```PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.DataBoxEdge
```

Az erőforrás-szolgáltatók regisztrálásával kapcsolatos további információkért lásd: [hibák elhárítása az erőforrás-szolgáltató regisztrálásához](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors).

## <a name="manage-connectivity-mode"></a>Kapcsolati mód kezelése

Az alapértelmezett normál mód mellett az eszköz részlegesen leválasztott vagy leválasztott módban is futhat. Az alábbi módok mindegyikét az alábbiak szerint írja le:

- **Részben leválasztva** – ebben a módban az eszköz nem tudja feltölteni a megosztásokra vonatkozó összes adatfeltöltést, azonban a Azure Portal használatával felügyelhető.

    Ezt a módot általában a mért műhold-hálózaton használják, és a cél a hálózati sávszélesség-fogyasztás csökkentése. Az eszköz-figyelési műveletek esetében a minimális hálózati fogyasztás továbbra is előfordulhat.

- **Leválasztva** – ebben a módban az eszköz teljesen le van választva a felhőből, és a Felhőbeli feltöltések és a letöltések is le vannak tiltva. Az eszközt csak a helyi webes felületen keresztül lehet felügyelni.

    Ez a mód általában akkor használatos, ha az eszközt offline állapotba kívánja helyezni.

Az eszköz üzemmódjának módosításához kövesse az alábbi lépéseket:

1. Az eszköz helyi webes FELÜLETén nyissa meg a **konfigurációs > a felhő beállításait**.
2. Tiltsa le a **Felhőbeli feltöltést és letöltést**.
3. Az eszköz részlegesen leválasztott módban való futtatásához engedélyezze **Azure Portal felügyeletet**.

    ![Kapcsolati mód](media/data-box-gateway-manage-access-power-connectivity-mode/connectivity-mode-1.png)
 
4. Az eszköz leválasztott módban való futtatásához tiltsa le a **Azure Portal felügyeletet**. Most az eszközt csak a helyi webes felületen keresztül lehet felügyelni.

    ![Kapcsolati mód](media/data-box-gateway-manage-access-power-connectivity-mode/connectivity-mode-2.png)

## <a name="manage-power"></a>A Power kezelése

Leállíthatja vagy újraindíthatja a virtuális eszközt a helyi webes felhasználói felület használatával. Az újraindítás előtt ajánlott offline állapotba állítani a gazdagépen található megosztásokat, majd magát az eszközt is. Ezzel a lehető legkisebbre csökkenti az adatsérülés esélyét.

1. A helyi webes KEZELŐFELÜLETen lépjen a **karbantartás > energiagazdálkodási beállítások**elemre.
2. A kívánt művelettől függően kattintson a **Leállítás** vagy az **Újraindítás** lehetőségre.

    ![Energiaellátási beállítások](media/data-box-gateway-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. Ha a rendszer megerősítést kér, kattintson az **Igen** gombra a folytatáshoz.

> [!NOTE]
> Ha leállítja a virtuális eszközt, az eszközt a hypervisor-felügyeleten keresztül kell elindítania.
