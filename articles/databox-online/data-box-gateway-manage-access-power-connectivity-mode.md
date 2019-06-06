---
title: A Microsoft Azure Data Box Gateway eszközhöz való hozzáféréshez, a teljesítmény és a csatlakozási mód |} A Microsoft Docs
description: Ismerteti, hogyan kezelheti a hozzáférést, a teljesítmény és a kapcsolódási módja az, hogy a segítségével adatátvitel Azure-bA az Azure Data Box Gateway eszköz
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 06/03/2019
ms.author: alkohli
ms.openlocfilehash: 44343f6bc6f48a6caa056f3336af55613a1e74d0
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66476797"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-data-box-gateway"></a>Az Azure Data Box Gateway kezelheti a hozzáférést, a teljesítmény és a csatlakozási mód

Ez a cikk ismerteti a hozzáférést, a teljesítmény és a csatlakozási mód kezelése az Azure Data Box Gateway számára. Ezeket a műveleteket a helyi webes felhasználói felületen vagy az Azure Portalon keresztül történik.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Eszköz-hozzáférés kezelése
> * Csatlakozási mód kezelése
> * Energiagazdálkodás

## <a name="manage-device-access"></a>Eszköz-hozzáférés kezelése

A Data Box átjáróeszköz eléréséhez a jelszó használatát vezérlik majd. Módosíthatja a jelszót a helyi webes felhasználói felületen. Az eszköz jelszavát az Azure Portalon is alaphelyzetbe állíthatja.

### <a name="change-device-password"></a>Az eszköz jelszavának módosítása

Kövesse az alábbi lépéseket a helyi felhasználói felületen, az eszköz jelszavának módosítása.

1. A helyi webes felhasználói felületen váltson **karbantartási > jelszó módosítása**.
2. Adja meg a jelenlegi jelszavát, majd az új jelszót. A megadott jelszónak 8 – 16 karakter hosszúságúnak kell lennie. A következő karakterek közül 3 a jelszóban: nagybetű, kisbetű, számjegyeket és speciális karaktereket. Az új jelszót.

    ![Jelszó módosítása](media/data-box-gateway-manage-access-power-connectivity-mode/change-password-1.png)

3. Kattintson a **jelszó módosítása**.
 
### <a name="reset-device-password"></a>Az eszköz jelszavának alaphelyzetbe állítása

A visszaállítási munkafolyamat nem szükséges majd előkeresnie a régi jelszót, és akkor hasznos, ha a jelszó elvész. Ez a munkafolyamat az Azure Portalon történik.

1. Az Azure Portalon lépjen a **áttekintés > rendszergazdai jelszó alaphelyzetbe állítása**.

    ![Másik jelszó kérése](media/data-box-gateway-manage-access-power-connectivity-mode/reset-password-1.png)

 
2. Adja meg az új jelszót, és erősítse azt. A megadott jelszónak 8 – 16 karakter hosszúságúnak kell lennie. A következő karakterek közül 3 a jelszóban: nagybetű, kisbetű, számjegyeket és speciális karaktereket. Kattintson a **alaphelyzetbe**.

    ![Másik jelszó kérése](media/data-box-gateway-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-resource-access"></a>Erőforrás-hozzáférés kezelése

A Data Box Edge/Data Box átjáró, IoT Hub és az Azure Storage-erőforrások létrehozásához, egy erőforráscsoport szintjén közreműködője vagy újabb engedélyre van szükségük. Emellett a megfelelő erőforrás-szolgáltatók regisztrálása. Bármely művelethez használható aktiválási kulcs és a hitelesítő adatokat az Azure Active Directory Graph API-hoz való engedélyek is szükségesek. A következő szakaszokban ezekről.

### <a name="manage-microsoft-azure-active-directory-graph-api-permissions"></a>A Microsoft Azure Active Directory Graph API-engedélyek kezelése

A Data Box peremhálózati eszköz, vagy a hitelesítő adatokat igénylő valamilyen műveletet hajt végre az aktiválási kulcs létrehozásakor az Azure Active Directory Graph API engedélyre van szükségük. A hitelesítő adatokat igénylő műveleteket lehet:

-  Egy fájlmegosztás létrehozása egy kapcsolódó tárfiók.
-  Az eszközön a megosztások hozzáféréssel rendelkező felhasználó létrehozása.

Rendelkeznie kell egy `User` tudni igény szerint az Active Directory-bérlő eléréséhez `Read all directory objects`. Nem lehet a vendégfelhasználó, mivel azok nem rendelkezik engedélyekkel `Read all directory objects`. Ha Ön egy Vendég, majd a műveletek, például az aktiválási kulcs, a Data Box Edge-eszközön a megosztás létrehozása egy felhasználó létrehozása az összes sikertelen lesz.

Hozzáférést biztosítania a felhasználóknak, hogy az Azure Active Directory Graph API-val kapcsolatos további információkért lásd: [rendszergazdák, a felhasználók és a vendégfelhasználók hozzáférés alapértelmezett](https://docs.microsoft.com/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-).

### <a name="register-resource-providers"></a>Erőforrás-szolgáltató regisztrálása

Üzembe helyez egy erőforrást az Azure-ban (az Azure Resource Manager-modell), erőforrás-szolgáltató, amely támogatja az adott erőforráshoz van szükség. Ha például a virtuális gép kiépítése kell "Microsoft.Compute" erőforrás-szolgáltatót az előfizetésben elérhető.
 
Erőforrás-szolgáltató regisztrálva van az előfizetés mértékét a. Alapértelmezés szerint minden új Azure-előfizetést a leggyakrabban használt erőforrás-szolgáltatók listája előre regisztrált. A "Microsoft.DataBoxEdge" erőforrás-szolgáltató nem szerepel a listában.

Hozzáférési engedélyeket az előfizetés szintjéről a felhasználók számára az erőforrásokhoz, mint "Microsoft.DataBoxEdge" belül a tulajdonosi engedélyekkel rendelkeznek, az erőforrás-csoportok létrehozásához, amennyiben az erőforrás-szolgáltatók számára ezen erőforrások már nem szükséges regisztrálva.

Mielőtt megkísérli létrehozni minden olyan erőforrást, ellenőrizze, hogy az erőforrás-szolgáltató regisztrálva van az előfizetésben. Ha az erőforrás-szolgáltató nincs regisztrálva, szüksége lesz, győződjön meg arról, hogy az új erőforrás létrehozása a felhasználó rendelkezik-e elegendő jogokkal az előfizetés szintjén a szükséges erőforrás-szolgáltató regisztrálásához. Ha ez nem is tette, megjelenik a következő hibával:

*Az előfizetés <Subscription name> nem rendelkezik jogosultsággal erőforrás-szolgáltató(k) regisztrálása: Microsoft.DataBoxEdge.*


Az aktuális előfizetésben regisztrált erőforrás-szolgáltatók listájának lekéréséhez futtassa a következő parancsot:

```PowerShell
Get-AzResourceProvider -ListAvailable |where {$_.Registrationstate -eq "Registered"}
```

A Data Box peremhálózati eszköz `Microsoft.DataBoxEdge` kell regisztrálni. Regisztrálni `Microsoft.DataBoxEdge`, előfizetés rendszergazdája a következő parancsot kell futtatni:

```PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.DataBoxEdge
```

Erőforrás-szolgáltató regisztrálásával kapcsolatos további információkért lásd: [ki a hibákat az erőforrás-szolgáltatói regisztráció](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors).

## <a name="manage-connectivity-mode"></a>Csatlakozási mód kezelése

Az alapértelmezett Normál mód szereplőkkel eszközét is futtathatja részlegesen leválasztott vagy kapcsolat nélküli üzemmódban. Az egyes módokban, az alábbiakban ismertetjük:

- **Részlegesen csatlakoztatva** – ebben a módban az eszközt nem tölthetők fel bármely azonban az adatokat a megosztásokat az Azure-portálon keresztül kezelhetők.

    Ebben a módban általában szolgál a hálózati forgalmi díjas műholdas, és a cél, hogy minimalizálja a hálózati sávszélesség-használatot. Minimális hálózatleterheltség figyelési műveletekhez eszköz így is előfordulhatnak.

- **Leválasztott** – ebben a módban az eszköz teljes mértékben megszakad a felhő és mind a felhőbeli feltöltések, letöltések le vannak tiltva. Az eszköz csak a helyi webes felhasználói felületen felügyelhető.

    Ebben a módban általában használnak, az eszköz offline állapotúra szeretné állítani.

Az eszköz üzemmódjának módosítása, kövesse az alábbi lépéseket:

1. A helyi webes felhasználói felületen, az eszköz, lépjen a **Configuration > Felhőbeállítások**.
2. Tiltsa le a **felhőalapú feltöltési és letöltési**.
3. Az eszköz részlegesen leválasztott módban fut, engedélyezze az **az Azure portal felügyeleti**.

    ![Csatlakozási mód](media/data-box-gateway-manage-access-power-connectivity-mode/connectivity-mode-1.png)
 
4. Az eszköz leválasztott módban fusson, tiltsa le a **az Azure portal felügyeleti**. Most már az eszköz csak kezelhetők a helyi webes felhasználói felületen.

    ![Csatlakozási mód](media/data-box-gateway-manage-access-power-connectivity-mode/connectivity-mode-2.png)

## <a name="manage-power"></a>Energiagazdálkodás

Állítsa le, vagy indítsa újra a virtuális eszköz helyi webes felhasználói felület használatával. Az újraindítás előtt ajánlott offline állapotba állítani a gazdagépen található megosztásokat, majd magát az eszközt is. Ez a művelet minimálisra csökkenti a adatsérülés lehetőségét.

1. A helyi webes felhasználói felületen váltson **karbantartási > energiaellátási beállítások**.
2. Kattintson a **leállítási** vagy **indítsa újra a** függően, hogy mit kíván tenni.

    ![Energiaellátási beállítások](media/data-box-gateway-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. Amikor a rendszer megerősítést kér, kattintson a **Igen** a folytatáshoz.

> [!NOTE]
> Ha a virtuális eszköz leállítása, szüksége lesz a indítsa el az eszközt a hipervizor-management szolgáltatáson keresztül.
