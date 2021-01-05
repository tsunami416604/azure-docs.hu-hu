---
title: BareMetal-példányok egységei az Azure-ban
description: Ismerje meg, hogyan azonosíthatja és kezelheti a BareMetal-példányok egységeit a Azure Portalon keresztül.
ms.topic: how-to
ms.date: 12/31/2020
ms.openlocfilehash: 927baa79519781ef74920b17bc9fcd858f0f6c6f
ms.sourcegitcommit: 42922af070f7edf3639a79b1a60565d90bb801c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/31/2020
ms.locfileid: "97829188"
---
# <a name="manage-baremetal-instances-through-the-azure-portal"></a>Operációs rendszer nélküli példányok kezelése az Azure Portalon
 
Ez a cikk bemutatja, hogyan jeleníti meg a [Azure Portal](https://portal.azure.com/) a [BareMetal-példányokat](baremetal-overview-architecture.md). Ez a cikk azokat a tevékenységeket mutatja be, amelyeket a Azure Portal az üzembe helyezett BareMetal-példányok egységével végezhet el. 
 
## <a name="register-the-resource-provider"></a>Az erőforrás-szolgáltató regisztrálása
A BareMetal-példányok Azure-erőforrás-szolgáltatója biztosítja a Azure Portalban lévő példányok láthatóságát, amely jelenleg nyilvános előzetes verzióban érhető el. Alapértelmezés szerint a BareMetal példányok üzembe helyezéséhez használt Azure-előfizetés regisztrálja a *BareMetalInfrastructure* erőforrás-szolgáltatót. Ha nem látja a telepített BareMetal-példányok egységét, regisztrálnia kell az erőforrás-szolgáltatót az előfizetésében. Az BareMetal-példány erőforrás-szolgáltatóját kétféleképpen lehet regisztrálni:
 
* [Azure CLI](#azure-cli)
 
* [Azure Portal](#azure-portal)
 
### <a name="azure-cli"></a>Azure CLI
 
Jelentkezzen be a BareMetal példány üzembe helyezéséhez használt Azure-előfizetésbe az Azure CLI-n keresztül. A BareMetalInfrastructure erőforrás-szolgáltatót az alábbiak szerint regisztrálhatja:

```azurecli-interactive
az provider register --namespace Microsoft.BareMetalInfrastructure
```
 
További információ: [Azure Resource Providers és types](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell).
 
### <a name="azure-portal"></a>Azure Portal
 
A BareMetalInfrastructure erőforrás-szolgáltatót a Azure Portal keresztül regisztrálhatja.
 
Az előfizetést fel kell sorolnia a Azure Portalban, majd duplán kell kattintania a BareMetal-példány-egységek üzembe helyezéséhez használt előfizetésre.
 
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Az Azure Portal menüjében válassza a **Minden szolgáltatás** lehetőséget.

1. A **minden szolgáltatás** mezőben adja meg az **előfizetés** elemet, majd válassza az **előfizetések** lehetőséget.

1. Válassza ki az előfizetést az előfizetés listából a megtekintéshez.

1. Válassza az **erőforrás-szolgáltatók** lehetőséget, és írja be a **BareMetalInfrastructure** a keresésbe. Az erőforrás-szolgáltatót **regisztrálni** kell, ahogy a képen látható.
 
>[!NOTE]
>Ha az erőforrás-szolgáltató nincs regisztrálva, válassza a **regisztráció** lehetőséget.
 
:::image type="content" source="media/baremetal-infrastructure-portal/register-resource-provider-azure-portal.png" alt-text="Képernyőkép, amely megjeleníti a regisztrált BareMetal-példány egységet":::
 
## <a name="baremetal-instance-units-in-the-azure-portal"></a>BareMetal-példányok egységei a Azure Portal
 
BareMetal-példány központi telepítési kérelmének elküldésekor meg kell adnia azt az Azure-előfizetést, amelyhez az BareMetal-példányokhoz csatlakozik. Használja ugyanazt az előfizetést, amelyet az BareMetal-példányok egységeit használó alkalmazás rétegének telepítéséhez használ.
 
A BareMetal-példányok üzembe helyezése során új [Azure-erőforráscsoport](../../../azure-resource-manager/management/manage-resources-portal.md) jön létre a telepítési kérelemben használt Azure-előfizetésben. Ez az új erőforráscsoport felsorolja az adott előfizetésben üzembe helyezett összes BareMetal-példány egységét.

1. Az BareMetal-előfizetés Azure Portaljában válassza az **erőforráscsoportok** lehetőséget.
 
   :::image type="content" source="media/baremetal-infrastructure-portal/view-baremetal-instance-units-azure-portal.png" alt-text="Az erőforráscsoportok listáját megjelenítő képernyőkép":::

1. Keresse meg az új erőforráscsoportot a listában.
 
   :::image type="content" source="media/baremetal-infrastructure-portal/filter-resource-groups.png" alt-text="A szűrt erőforráscsoportok listájában szereplő BareMetal-példány egységét megjelenítő képernyőkép" lightbox="media/baremetal-infrastructure-portal/filter-resource-groups.png":::
   
   >[!TIP]
   >Szűrheti a BareMetal-példány üzembe helyezéséhez használt előfizetést. A megfelelő előfizetésre való szűrés után előfordulhat, hogy az erőforráscsoportok hosszú listája van. Keressen egy javítást a **TXXX** , ahol XXX három számjegy, például a **-T250**.

1. A részletek megjelenítéséhez válassza ki az új erőforráscsoportot. A képen egy telepített BareMetal-példány egység látható.
   
   >[!NOTE]
   >Ha több BareMetal-példány bérlőjét telepítette ugyanazon az Azure-előfizetésen belül, több Azure-erőforráscsoport is megjelenhet.
 
## <a name="view-the-attributes-of-a-single-instance"></a>Egyetlen példány attribútumainak megtekintése
 
Megtekintheti egy adott egység részleteit. A BareMetal-példány listájában válassza ki a megtekinteni kívánt egyetlen példányt.
 
:::image type="content" source="media/baremetal-infrastructure-portal/view-attributes-single-baremetal-instance.png" alt-text="Egy adott példány BareMetal-Példányi egységének attribútumait megjelenítő képernyőkép" lightbox="media/baremetal-infrastructure-portal/view-attributes-single-baremetal-instance.png":::
 
A rendszerképben szereplő attribútumok nem sokban különböznek az Azure virtuális gép (VM) attribútumaitól. A bal oldalon láthatja az erőforráscsoportot, az Azure-régiót és az előfizetés nevét és AZONOSÍTÓját. Ha címkéket rendelt hozzá, akkor itt is láthatja őket. Alapértelmezés szerint a BareMetal-példányok egységei nem rendelkeznek hozzárendelt címkékkel.
 
A jobb oldalon megjelenik az egység neve, az operációs rendszer (OS), az IP-cím és az SKU, amely a CPU-szálak és a memória számát jeleníti meg. Ekkor megjelenik az energiagazdálkodási állapot és a hardver verziója is (az BareMetal-példány bélyegzője). A tápellátási állapot azt jelzi, hogy a hardvereszköz be van-e kapcsolva vagy ki van-e kapcsolva. Az operációs rendszer részletei azonban nem jelzik, hogy működik-e.
 
A lehetséges hardver-változatok a következők:
 
* 3. változat
 
* 4. változat
 
* 4,2-es változat
 
>[!NOTE]
>A 4,2-es változat a legújabb rebranded BareMetal-infrastruktúra a 4. változat architektúrán keresztül. Jelentős mértékben javult az Azure-beli virtuális gépek és a 4. változatban vagy sorokban üzembe helyezett BareMetal-példányok közötti hálózati késés. További információ a különböző változatokról: BareMetal-infrastruktúra az [Azure](baremetal-overview-architecture.md)-ban.
 
Emellett a jobb oldalon megtalálja az [Azure Proximity-elhelyezési csoport](../../../virtual-machines/linux/co-location.md) nevét, amely automatikusan létrejön az egyes üzembe helyezett BareMetal-példányok egysége számára. Az alkalmazás réteget futtató Azure-beli virtuális gépek üzembe helyezése esetén hivatkozzon a közelségi csoportra. Ha az BareMetal-példány egységéhez tartozó közelségi elhelyezési csoportot használja, győződjön meg arról, hogy az Azure-beli virtuális gépek üzembe helyezése a BareMetal-példány egységéhez közel van.
 
>[!TIP]
>Ha az alkalmazás rétegét ugyanabban az Azure-adatközpontban szeretné megkeresni, mint a 4. x változatot, tekintse meg az [Azure Proximity elhelyezési csoportok az optimális hálózati késés](../../../virtual-machines/workloads/sap/sap-proximity-placement-scenarios.md)érdekében
 
## <a name="check-activities-of-a-single-instance"></a>Egyetlen példány tevékenységének ellenőrzését
 
Egyetlen egység tevékenységeit is megtekintheti. A legfontosabb tevékenységek közül az egyik az egység újraindítása. A felsoroltak közé tartozik a tevékenység állapota, az elindított tevékenység időbélyege, az előfizetés azonosítója, valamint a tevékenységet indító Azure-felhasználó.
 
:::image type="content" source="media/baremetal-infrastructure-portal/check-activities-single-baremetal-instance.png" alt-text="A BareMetal-példány egységét bemutató képernyőkép" lightbox="media/baremetal-infrastructure-portal/check-activities-single-baremetal-instance.png":::
 
Az egység metaadatait az Azure-ban a tevékenység naplójában is rögzíti a rendszer. Az újraindítás után megtekintheti az **írási BareMetallnstances** tevékenységeit. Ez a tevékenység nem változtatja meg magát a BareMetal-példány egységén, de az egység metaadatait az Azure-ban dokumentálja.
 
Egy másik tevékenység, amely rögzítésre kerül, amikor hozzáad vagy töröl egy [címkét](../../../azure-resource-manager/management/tag-resources.md) egy példányhoz.
 
## <a name="add-and-delete-an-azure-tag-to-an-instance"></a>Azure-címke hozzáadása és törlése egy példányhoz
 
Hozzáadhat Azure-címkéket egy BareMetal-példány-egységhez, vagy törölheti is azokat. A címkék hozzárendelésének módja nem különbözik a címkék virtuális gépekhez való hozzárendelésének módjától. A virtuális gépekhez hasonlóan a címkék az Azure-metaadatokban, a BareMetal-példányok esetében ugyanazok a korlátozások vonatkoznak, mint a virtuális gépek címkéi.
 
A címkék törlése ugyanúgy történik, mint a virtuális gépeken. A címkék alkalmazásával és törlésével kapcsolatban a BareMetal-példány egységének tevékenységi naplójában szerepel.
 
## <a name="check-properties-of-an-instance"></a>Példány tulajdonságainak megtekintése
 
A példányok beolvasásakor a Properties (Tulajdonságok) szakaszra kattintva megtekintheti a példányokkal kapcsolatban gyűjtött adatokat. Az összegyűjtött adatok közé tartozik az Azure-kapcsolat, a tárolási háttér, a ExpressRoute áramkör-azonosító, az egyedi erőforrás-azonosító és az előfizetés-azonosító. Ezeket az információkat a támogatási kérelmekben vagy a tárolási Pillanatképek konfigurációjának beállításakor fogja használni.
 
Egy másik fontos információ, amelyet látni fog a Storage NFS IP-címe. Elkülöníti a tárolót a **bérlőhöz** a BareMetal-példány veremben. Ezt az IP-címet akkor fogja használni, ha szerkeszti a [tárolási Pillanatképek biztonsági másolatait tartalmazó konfigurációs fájlt](../../../virtual-machines/workloads/sap/hana-backup-restore.md#set-up-storage-snapshots).
 
:::image type="content" source="media/baremetal-infrastructure-portal/baremetal-instance-properties.png" alt-text="A BareMetal-példány tulajdonságainak beállításait megjelenítő képernyőkép" lightbox="media/baremetal-infrastructure-portal/baremetal-instance-properties.png":::
 
## <a name="restart-a-unit-through-the-azure-portal"></a>Egység újraindítása a Azure Portal
 
Különböző helyzetekben az operációs rendszer nem fejezi be az újraindítást, amelyhez az BareMetal-példány egységének újraindítása szükséges. Az egység újraindítását közvetlenül a Azure Portalról végezheti el:
 
Válassza az **Újraindítás** lehetőséget, majd az **Igen** gombot az egység újraindításának megerősítéséhez.
 
:::image type="content" source="media/baremetal-infrastructure-portal/baremetal-instance-restart.png" alt-text="A BareMetal-példány egységének újraindítását bemutató képernyőkép":::
 
Egy BareMetal-példány egységének újraindításakor késést tapasztalhat. Ebben a késésben a tápellátás a kezdeti kezdéstől **kezdve kezdődik** , ami azt jelenti **, hogy az** operációs rendszer teljesen elindult. Ennek eredményeképpen a rendszer az újraindítást követően nem tud bejelentkezni az egységbe, amint az állapot **elindul**.
 
>[!IMPORTANT]
>A BareMetal-példány egységében lévő memória mennyiségétől függően a hardver újraindítása és újraindítása, valamint az operációs rendszer akár egy órát is igénybe vehet.
 
## <a name="open-a-support-request-for-baremetal-instances"></a>Támogatási kérelem megnyitása BareMetal-példányokhoz
 
A támogatási kérelmeket kifejezetten egy BareMetal-példány egységéhez is elküldheti.
1. Azure Portal a Súgó és **támogatás** területen hozzon létre egy **[új támogatási kérést](https://rc.portal.azure.com/#create/Microsoft.Support)** , és adja meg a következő információkat a jegyhez:
 
   - **Probléma típusa:** Probléma típusának kiválasztása
 
   - **Előfizetés:** Előfizetés kiválasztása
 
   - **Szolgáltatás:** BareMetal-infrastruktúra
 
   - **Erőforrás:** Adja meg a példány nevét
 
   - **Összefoglalás:** Adja meg a kérés összegzését
 
   - **Probléma típusa:** Probléma típusának kiválasztása
 
   - **Probléma altípusa:** A probléma altípusának kiválasztása

1. Válassza a **megoldások** fület a probléma megoldásának megkereséséhez. Ha nem talál megoldást, folytassa a következő lépéssel.

1. Válassza a **részletek** lapot, és adja meg, hogy a probléma a virtuális gépekkel vagy a BareMetal-példányok egységével kapcsolatos-e. Ez az információ segít a támogatási kérelemnek a megfelelő szakértőknek történő irányításában.

1. Jelezze, hogy mikor kezdődött el a probléma, és válassza ki a példány régióját.

1. Adja meg a kérés további részleteit, és szükség esetén töltse fel a fájlt.

1. Válassza a **felülvizsgálat + létrehozás** lehetőséget a kérelem elküldéséhez.
 
Egy támogatási képviselő legfeljebb öt munkanapot vesz igénybe, hogy erősítse meg a kérését.

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a munkaterhelésekről, tekintse meg a [BareMetal számítási feladatok típusai](../../../virtual-machines/workloads/sap/get-started.md)című témakört.