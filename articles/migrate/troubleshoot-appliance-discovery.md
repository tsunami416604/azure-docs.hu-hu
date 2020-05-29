---
title: Azure Migrate berendezés üzembe helyezésének és felderítésének hibáinak megoldása
description: Segítség kérése a Azure Migrate berendezés üzembe helyezéséhez és a gépek felfedéséhez.
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.topic: troubleshooting
ms.date: 01/02/2020
ms.openlocfilehash: 4559cd40bf9482ece80d0b6123ca73b7a0cbb3aa
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/29/2020
ms.locfileid: "84170902"
---
# <a name="troubleshoot-the-azure-migrate-appliance-and-discovery"></a>A Azure Migrate készülék és a felderítés hibáinak megoldása

Ez a cikk segítséget nyújt a [Azure Migrate](migrate-services-overview.md) berendezés telepítésekor felmerülő problémák megoldásában, és a berendezés használatával felderíti a helyszíni gépeket.


## <a name="whats-supported"></a>Mik támogatottak?

[Tekintse át](migrate-appliance.md) a berendezések támogatási követelményeit.


## <a name="invalid-ovf-manifest-entry"></a>"Érvénytelen OVF manifest-bejegyzés"

Ha "a megadott jegyzékfájl érvénytelen: érvénytelen OVF manifest-bejegyzés" hibaüzenet jelenik meg, tegye a következőket:

1. Ellenőrizze, hogy a Azure Migrate készülék PETESEJTJEInek fájlja helyesen van-e letöltve a kivonatoló értékének ellenőrzésével. [További információ](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware). Ha a kivonatoló érték nem egyezik, töltse le újra a PETESEJT-fájlt, majd próbálja megismételni a telepítést.
2. Ha a telepítés továbbra is meghiúsul, és a VMware vSphere ügyfél használatával telepíti a OVF-fájlt, próbálja meg telepíteni a vSphere webes ügyfelén keresztül. Ha az üzembe helyezés továbbra is sikertelen, próbálkozzon más böngészővel.
3. Ha a vSphere webes ügyfélprogramot használja, és vCenter Server 6,5-es vagy 6,7-es számítógépen szeretné telepíteni, próbálja meg közvetlenül telepíteni a PETESEJTeket az ESXi-gazdagépre:
   - Kapcsolódjon közvetlenül az ESXi-gazdagéphez (vCenter Server helyett) a webes ügyféllel (https://<*gazdagép IP-címe*>/UI).
   - A **saját**  >  **leltár**területen válassza a **fájl**  >  **központi telepítése OVF sablont**. Tallózással keresse meg a PETESEJTeket, és fejezze be a telepítést.
4. Ha az üzembe helyezés továbbra is sikertelen, forduljon Azure Migrate támogatási szolgálathoz.

## <a name="cant-connect-to-the-internet"></a>Nem lehet csatlakozni az internethez

Ez akkor fordulhat elő, ha a berendezés számítógépe proxy mögött van.

- Győződjön meg arról, hogy megadja az engedélyezési hitelesítő adatokat, ha a proxynak szüksége van rájuk.
- Ha URL-alapú tűzfal-proxyt használ a kimenő kapcsolatok vezérléséhez, adja hozzá [ezeket az URL-címeket](migrate-appliance.md#url-access) egy engedélyezési listához.
- Ha elfogó proxyt használ az internethez való csatlakozáshoz, [ezeket a lépéseket követve](https://docs.microsoft.com/azure/migrate/concepts-collector)importálja a tanúsítványt a berendezés virtuális gépén.

## <a name="cant-sign-into-azure-from-the-appliance-web-app"></a>Nem lehet bejelentkezni az Azure-ba a készülék webalkalmazásból

A "Sajnáljuk, de hiba történt a bejelentkezéskor" üzenet jelenik meg, ha a helytelen Azure-fiókot használja az Azure-ba való bejelentkezéshez. Ez a hiba néhány okból fordul elő:

- Ha bejelentkezik a készülék webalkalmazásba a nyilvános felhőben, a kormányzati felhő portál felhasználói fiókjának hitelesítő adataival.
- Ha a saját felhőalapú portál felhasználói fiókjának hitelesítő adataival jelentkezik be a kormányzati felhőbe.

Győződjön meg arról, hogy a megfelelő hitelesítő adatokat használja.

##  <a name="datetime-synchronization-error"></a>Dátum-és időszinkronizálási hiba

A dátum-és időszinkronizálással (802) kapcsolatos hiba azt jelzi, hogy a kiszolgáló órája több mint öt percen belül nem lehet szinkronizálva az aktuális idővel. A gyűjtő virtuális gép órájának módosítása a jelenlegi időpontra:

1. Nyisson meg egy rendszergazdai parancssort a virtuális gépen.
2. Az időzóna ellenõrzéséhez futtassa a **w32tm/TZ**.
3. Az idő szinkronizálásához futtassa a **w32tm resync**.


## <a name="unabletoconnecttoserver"></a>"UnableToConnectToServer"

Ha ezt a hibaüzenetet kapja, előfordulhat, hogy nem tud csatlakozni a (z) vCenter Server *servername*. com: 9443. A hiba részletei azt jelzik, hogy nem figyelhető végpont, `https://\*servername*.com:9443/sdk` amely fogadja az üzenetet.

- Győződjön meg arról, hogy a készülék legújabb verzióját futtatja-e. Ha nem, frissítse a készüléket a [legújabb verzióra](https://docs.microsoft.com/azure/migrate/concepts-collector).
- Ha a probléma továbbra is a legújabb verzióban következik be, előfordulhat, hogy a készülék nem tudja feloldani a megadott vCenter Server nevet, vagy a megadott port helytelen lehet. Alapértelmezés szerint, ha a port nincs megadva, a gyűjtő megpróbál csatlakozni a 443-as számú portszámhoz.

    1. Pingelje a *servername*. com-t a készülékről.
    2. Ha az 1. lépés meghiúsul, próbáljon meg csatlakozni a vCenter-kiszolgálóhoz az IP-cím használatával.
    3. Azonosítsa a vCenter Serverhoz való csatlakozáshoz használandó portszámot.
    4. Ellenőrizze, hogy a vCenter Server működik-e.


## <a name="error-6005260039-appliance-might-not-be-registered"></a>60052/60039-es hiba: lehet, hogy a berendezés nincs regisztrálva

- 60052-es hiba: "előfordulhat, hogy a készülék nem regisztrálható sikeresen a Azure Migrate projektben", ha a berendezés regisztrálásához használt Azure-fiók nem rendelkezik megfelelő engedélyekkel.
    - Győződjön meg arról, hogy a készülék regisztrálásához használt Azure-beli felhasználói fiók legalább közreműködői engedélyekkel rendelkezik az előfizetésben.
    - [További](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance---vmware) információ a szükséges Azure-szerepkörökről és-engedélyekről.
- 60039-es hiba: "előfordulhat, hogy a készülék nem regisztrálható sikeresen a Azure Migrate projektben" akkor fordulhat elő, ha a regisztráció sikertelen, mert a berendezés regisztrálásához használt Azure Migrate projekt nem található.
    - A Azure Portalon, és győződjön meg arról, hogy a projekt létezik az erőforráscsoporthoz.
    - Ha a projekt nem létezik, hozzon létre egy új Azure Migrate projektet az erőforráscsoporthoz, és regisztrálja újra a készüléket. [Ismerje meg, hogyan](https://docs.microsoft.com/azure/migrate/how-to-add-tool-first-time#create-a-project-and-add-a-tool) hozhat létre új projektet.

## <a name="error-6003060031-key-vault-management-operation-failed"></a>60030/60031-es hiba: a Key Vault felügyeleti művelet sikertelen volt.

Ha a 60030-es vagy a 60031-es hibát kapja, "az Azure Key Vault felügyeleti művelet sikertelen", tegye a következőket:
- Győződjön meg arról, hogy a készülék regisztrálásához használt Azure-beli felhasználói fiók legalább közreműködői engedélyekkel rendelkezik az előfizetésben.
- Győződjön meg arról, hogy a fiók rendelkezik hozzáféréssel a hibaüzenetben megadott kulcstartóhoz, majd próbálja megismételni a műveletet.
- Ha a probléma továbbra is fennáll, forduljon a Microsoft támogatási szolgálatához.
- [További](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance---vmware) információ a szükséges Azure-szerepkörökről és-engedélyekről.

## <a name="error-60028-discovery-couldnt-be-initiated"></a>60028-es hiba: nem sikerült elindítani a felderítést

60028-es hiba: "a felderítés hiba miatt nem indítható el. A gazdagépek vagy fürtök megadott listája nem tudta végrehajtani a műveletet, mert nem sikerült elindítani a felderítést a hiba miatt, mert a virtuális gép információinak elérése vagy beolvasása során probléma merült fel. A többi gazdagép hozzáadása sikeresen megtörtént.

- Adja hozzá a hibaüzenetben felsorolt gazdagépeket a **gazdagép hozzáadása** lehetőség használatával.
- Ha érvényesítési hiba történt, tekintse át a Szervizelési útmutatót a hibák kijavításához, majd próbálkozzon újra a **Mentés és a felderítés megkezdése** lehetőséggel.

## <a name="error-60025-azure-ad-operation-failed"></a>60025-es hiba: nem sikerült az Azure AD-művelet 
60025-es hiba: "az Azure AD-művelet nem sikerült. Hiba történt az Azure AD-alkalmazás létrehozásakor vagy frissítésekor: akkor következik be, amikor a felderítés elindításához használt Azure-felhasználói fiók eltér a berendezés regisztrálásához használt fióktól. Tegye a következők egyikét:

- Győződjön meg arról, hogy a felderítést kezdeményező felhasználói fiók megegyezik a berendezés regisztrálásához használttal.
- Adja meg Azure Active Directory alkalmazás hozzáférési engedélyeit ahhoz a felhasználói fiókhoz, amelyhez a felderítési művelet sikertelen.
- Törölje az Azure Migrate projekthez korábban létrehozott erőforráscsoportot. Hozzon létre egy másik erőforráscsoportot az újraindításhoz.
- [További](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance---vmware) információ Azure Active Directory alkalmazás engedélyeiről.


## <a name="error-50004-cant-connect-to-host-or-cluster"></a>50004-es hiba: nem lehet csatlakozni a gazdagéphez vagy a fürthöz

50004-es hiba: "nem lehet csatlakozni egy gazdagéphez vagy fürthöz, mert a kiszolgáló neve nem oldható fel. WinRM-hibakód: 0x803381B9 "akkor fordulhat elő, ha a berendezés Azure DNS szolgáltatása nem tudja feloldani a megadott fürtöt vagy állomásnevet.

- Ha ezt a hibát látja a fürtön, a fürt teljes tartományneve.
- Előfordulhat, hogy ez a hiba a fürtben lévő gazdagépek esetében is megjelenik. Ez azt jelzi, hogy a berendezés tud csatlakozni a fürthöz, de a fürt olyan állomásnévket ad vissza, amelyek nem teljes tartománynevek. A hiba megoldásához frissítse a gazdagépek fájlját a készüléken az IP-cím és az állomásnevek hozzárendelésének hozzáadásával:
    1. Nyissa meg a Jegyzettömböt rendszergazdaként.
    2. Nyissa meg a C:\Windows\System32\Drivers\etc\hosts fájlt.
    3. Adja hozzá az IP-címet és az állomásnevet egy sorban. Ismételje meg az összes olyan gazdagépet vagy fürtöt, ahol ez a hiba látható.
    4. Mentse és zárja be a hosts fájlt.
    5. Győződjön meg arról, hogy a készülék tud-e csatlakozni a gazdagépekhez a berendezés-kezelő alkalmazás használatával. 30 perc elteltével a Azure Portalban láthatja a gazdagépek legfrissebb információit.

## <a name="discovered-vms-not-in-portal"></a>Felderített virtuális gépek nem a portálon

Ha a felderítési állapot "felderítés folyamatban", de még nem látja a virtuális gépeket a portálon, várjon néhány percet:
- Egy VMware virtuális gép esetében körülbelül 15 percet vesz igénybe.
- A Hyper-V virtuális gépek felderítéséhez minden egyes hozzáadott állomás körülbelül két percet vesz igénybe.

Ha a várakozás után az állapot nem változik, válassza a **kiszolgálók** lapon a **frissítés** lehetőséget. Ennek meg kell jelennie a felderített kiszolgálók számának Azure Migrateban: kiszolgáló értékelése és Azure Migrate: kiszolgáló áttelepítése.

Ha ez nem működik, és a VMware-kiszolgálókat felkeresi:

- Győződjön meg arról, hogy a megadott vCenter-fiókhoz megfelelő engedélyek vannak beállítva, és legalább egy virtuális géphez hozzáfér.
- Azure Migrate nem képes felderíteni a VMware virtuális gépeket, ha a vCenter-fiókhoz hozzáférés van megadva a vCenter VM-mappa szintjén. [További](set-discovery-scope.md) információ a hatókör-felderítésről.

## <a name="vm-data-not-in-portal"></a>A virtuális gép nem a portálon

Ha a felderített virtuális gépek nem jelennek meg a portálon, vagy ha a VM-adat elavult, várjon néhány percet. Akár 30 percet is igénybe vehet, hogy a felderített virtuális gépek konfigurációs információi megjelenjenek a portálon. Eltarthat néhány órát az alkalmazásadatok változásainak megjelenítéséhez. Ha ez idő után nem állnak rendelkezésre adatsorok, próbálkozzon a frissítéssel, a következőképpen

1. A **kiszolgálók**  >  **Azure Migrate kiszolgáló értékelése**lapon válassza az **Áttekintés**lehetőséget.
2. A **kezelés**területen válassza a **Agent Health**lehetőséget.
3. Válassza az **ügynök frissítése**lehetőséget.
4. Várjon, amíg a frissítési művelet befejeződik. Ekkor megjelenik a naprakész információ.

## <a name="deleted-vms-appear-in-portal"></a>A törölt virtuális gépek megjelennek a portálon

Ha törli a virtuális gépeket, és azok továbbra is megjelennek a portálon, várjon 30 percet. Ha továbbra is megjelennek, frissítse a fentiekben leírtak szerint.

## <a name="common-app-discovery-errors"></a>Gyakori alkalmazás-felderítési hibák

Azure Migrate támogatja az alkalmazások, szerepkörök és szolgáltatások felderítését Azure Migrate használatával: Server Assessment. Az alkalmazás-felderítés jelenleg csak VMware esetén támogatott. [További](how-to-discover-applications.md) információ az alkalmazások felderítésének beállításával kapcsolatos követelményekről és lépésekről.

A szokásos alkalmazás-felderítési hibák a táblázatban vannak összegezve. 

**Hiba** | **Okozhat** | **Művelet**
--- | --- | --- 
10000: "nem sikerült felderíteni a kiszolgálóra telepített alkalmazásokat". | Ez akkor fordulhat elő, ha a gép operációs rendszere nem Windows vagy Linux. | Csak az alkalmazás-észlelés használata Windows/Linux rendszeren.
10001: "nem sikerült beolvasni a kiszolgálót telepített alkalmazások". | Belső hiba – néhány hiányzó fájl a berendezésben. | Vegye fel a kapcsolatot a Microsoft támogatási szolgálatával.
10002: "nem sikerült beolvasni a kiszolgálót telepített alkalmazások". | Előfordulhat, hogy a készülék felderítési ügynöke nem működik megfelelően. | Ha a probléma 24 órán belül nem oldódik meg, forduljon az ügyfélszolgálathoz.
10003 "nem sikerült beolvasni a kiszolgálót telepített alkalmazások". | Előfordulhat, hogy a készülék felderítési ügynöke nem működik megfelelően. | Ha a probléma 24 órán belül nem oldódik meg, forduljon az ügyfélszolgálathoz.
10004: "nem sikerült felderíteni a telepített alkalmazásokat <Windows/Linux> gépekhez." |  A <Windows/Linux> gépekhez való hozzáféréshez szükséges hitelesítő adatok nem voltak megadva a berendezésben.| Adjon hozzá egy hitelesítő adatot a készülékhez, amely hozzáféréssel rendelkezik a <Windows/Linux> gépekhez.
10005: "nem lehet hozzáférni a helyszíni kiszolgálóhoz". | Lehet, hogy a hozzáférési hitelesítő adatok helytelenek. | Frissítse a készülék hitelesítő adatait, és győződjön meg arról, hogy a megfelelő számítógéphez fér hozzá. 
10006: "nem lehet hozzáférni a helyszíni kiszolgálóhoz". | Ez akkor fordulhat elő, ha a gép operációs rendszere nem Windows vagy Linux.|  Csak az alkalmazás-észlelés használata Windows/Linux rendszeren.
10007: "nem sikerült feldolgozni a metaadatok lekérését" | Ez a belső hiba történt a JSON deszerializálására tett kísérlet során. | Megoldásért forduljon Microsoft ügyfélszolgálata
9000/9001/9002: "nem sikerült felderíteni a kiszolgálóra telepített alkalmazásokat". | Előfordulhat, hogy a VMware-eszközök nincsenek telepítve vagy sérültek. | Telepítse/Telepítse újra a VMware-eszközöket a megfelelő gépre, és győződjön meg róla, hogy fut.
9003: nem sikerült felderíteni a kiszolgálóra telepített alkalmazásokat ". | Ez akkor fordulhat elő, ha a gép operációs rendszere nem Windows vagy Linux. | Csak az alkalmazás-észlelés használata Windows/Linux rendszeren.
9004: "nem sikerült felderíteni a kiszolgálóra telepített alkalmazásokat". | Ez akkor fordulhat elő, ha a virtuális gép ki van kapcsolva. | A felderítéshez győződjön meg arról, hogy a virtuális gép be van kapcsolva.
9005: "nem sikerült felderíteni a virtuális gépre telepített alkalmazásokat. | Ez akkor fordulhat elő, ha a gép operációs rendszere nem Windows vagy Linux. | Csak az alkalmazás-észlelés használata Windows/Linux rendszeren.
9006/9007: "nem sikerült beolvasni a kiszolgálót telepített alkalmazások". | Előfordulhat, hogy a készülék felderítési ügynöke nem működik megfelelően. | Ha a probléma 24 órán belül nem oldódik meg, forduljon az ügyfélszolgálathoz.
9008: "nem sikerült beolvasni a kiszolgálót telepített alkalmazások". | Belső hiba lehet.  | TF a probléma 24 órán belül nem oldódik meg, forduljon az ügyfélszolgálathoz.
9009: "nem sikerült beolvasni a kiszolgálót telepített alkalmazások". | Akkor fordulhat elő, ha a kiszolgálón a Windows felhasználói fiókok felügyelete (UAC) beállításai korlátozóak, és megakadályozza a telepített alkalmazások felderítését. | Keressen rá a "felhasználói fiókok felügyelete" beállításokra a kiszolgálón, és konfigurálja az UAC-beállítást a kiszolgálón az alacsonyabb két szint egyikére.
9010: "nem sikerült beolvasni a kiszolgálót telepített alkalmazások". | Belső hiba lehet.  | TF a probléma 24 órán belül nem oldódik meg, forduljon az ügyfélszolgálathoz.
9011: "a vendégen letölthető fájl nem található a vendég virtuális gépen" | A probléma belső hiba miatt fordulhat elő. | A probléma 24 órán belül automatikusan fel lesz oldva. Ha a probléma továbbra is fennáll, vegye fel a kapcsolatot Microsoft ügyfélszolgálata.
9012: "az eredmény fájljának tartalma üres." | A probléma belső hiba miatt fordulhat elő. | A probléma 24 órán belül automatikusan fel lesz oldva. Ha a probléma továbbra is fennáll, vegye fel a kapcsolatot Microsoft ügyfélszolgálata.
9013: "új ideiglenes profil jön létre a VMware virtuális géphez való minden bejelentkezéshez" | A rendszer minden bejelentkezéshez új ideiglenes profilt hoz létre a virtuális gépen. | Győződjön meg arról, hogy a vendég virtuális gép hitelesítő adataiban megadott Felhasználónév UPN formátumban van megadva. Miután módosította a vendég virtuális gép hitelesítő adataiban megadott felhasználónevet UPN formátumra, a tálcán a Feladatkezelő eszközben indítsa újra a Microsoft Azure VMWare Discovery szolgáltatást az új felderítés megkereséséhez.
9015: "nem lehet csatlakozni a VMware virtuális gépekhez, mert nincs megfelelő jogosultsága a vCenter" | A vendég műveleti szerepkör nincs engedélyezve a vCenter felhasználói fiókon. | Győződjön meg arról, hogy a vendég műveleti szerepkör engedélyezve van a vCenter felhasználói fiókján.
9016: "nem sikerült csatlakozni a VMware virtuális gépekhez, mert a vendég műveleti ügynök kívül esik az adatokon" | A VMware-eszközök nincsenek megfelelően telepítve vagy nem naprakészek. | Győződjön meg arról, hogy a VMware-eszközök megfelelően vannak telepítve és naprakészek.
9017: "a felderített metaadatokat tartalmazó fájl nem található a virtuális gépen." | A probléma belső hiba miatt fordulhat elő. | Megoldásért forduljon Microsoft ügyfélszolgálatahoz.
9018: "a PowerShell nincs telepítve a vendég virtuális gépeken." | A PowerShell nem érhető el a vendég virtuális gépen. | Telepítse a PowerShellt a vendég virtuális gépen.
9019: "nem sikerült felderíteni a vendég virtuális gép műveletének hibája miatt" | A VMware Guest művelet sikertelen volt a virtuális gépen. | Győződjön meg arról, hogy a virtuális gép hitelesítő adatai érvényesek, és a vendég virtuális gép hitelesítő adataiban megadott Felhasználónév UPN formátumban van megadva.
9020: "a fájl létrehozása engedély megtagadva." | A felhasználóhoz vagy a Csoportházirendhez társított szerepkör korlátozza a felhasználót, hogy hozza létre a fájlt a mappában. | Ellenőrizze, hogy a megadott vendég felhasználó rendelkezik-e létrehozás engedéllyel a fájlhoz a mappában. A mappa nevében tekintse meg az **értesítések** a kiszolgáló értékelése című témakört.
9021: "a fájl létrehozása engedély megtagadva a mapparendszer ideiglenes elérési útján". | A VMware-eszköz verziója nem támogatott a virtuális gépen | Frissítse a VMware-eszköz verzióját a 10.2.0 felett.
9022: "a WMI-objektum elérésének beolvasása megtagadva." | A felhasználóhoz vagy a Csoportházirendhez társított szerepkör korlátozza a felhasználót a WMI-objektum elérésére. | Vegye fel a kapcsolatot Microsoft ügyfélszolgálata.
9023: "a rendszergyökér környezeti változó értéke üres." | Ismeretlen | Vegye fel a kapcsolatot Microsoft ügyfélszolgálata.
9024: "a TEMP környezeti változó értéke üres." | Ismeretlen | Vegye fel a kapcsolatot Microsoft ügyfélszolgálata.
9025: "a PowerShell sérült a vendég virtuális gépeken." | Ismeretlen | Telepítse újra a PowerShellt a vendég virtuális gépen, és ellenőrizze, hogy a PowerShell futtatható-e a vendég virtuális gépen.
8084: "nem sikerült felderíteni az alkalmazásokat VMware-hiba miatt:  <Exception from VMware> " | Az Azure Migrate készülék VMware API-kat használ az alkalmazások felderítéséhez. Ez a probléma akkor fordulhat elő, ha a vCenter Server kivételt okoz az alkalmazások felderítésére tett kísérlet során. A VMware hibaüzenet jelenik meg a portálon megjelenített hibaüzenetben. | Keresse meg az üzenetet a [VMware-dokumentációban](https://pubs.vmware.com/vsphere-51/topic/com.vmware.wssdk.apiref.doc/index-faults.html), és kövesse a lépéseket a javításhoz. Ha nem tudja kijavítani, forduljon a Microsoft ügyfélszolgálatához.



## <a name="next-steps"></a>Következő lépések
Állítson be egy berendezést a [VMware](how-to-set-up-appliance-vmware.md), a [Hyper-V](how-to-set-up-appliance-hyper-v.md)vagy a [fizikai kiszolgálók](how-to-set-up-appliance-physical.md)számára.
