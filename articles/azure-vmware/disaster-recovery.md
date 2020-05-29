---
title: A virtuális gépek vész-helyreállításának befejezése
description: Ez a cikk bemutatja, hogyan végezheti el a virtuális gépek vész-helyreállítását az AVS használatával
ms.topic: how-to
ms.date: 05/04/2020
ms.openlocfilehash: 5fdfc252486d94fcc22ebba9705fa5e259539921
ms.sourcegitcommit: 2721b8d1ffe203226829958bee5c52699e1d2116
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/28/2020
ms.locfileid: "84148157"
---
# <a name="complete-a-disaster-recovery-of-virtual-machines-using-azure-vmware-solution"></a>A virtuális gépek vész-helyreállításának befejezése az Azure VMware-megoldás használatával

Ez a cikk a VMware Hybrid Cloud Extension (HCX) megoldással rendelkező virtuális gépek vész-helyreállítási folyamatát tartalmazza, és egy Azure VMware-megoldás saját felhőjét használja helyreállítási vagy célhelyként.

A VMware HCX különböző műveleteket biztosít, amelyek a replikációs házirendekben részletes szabályozást és részletességet biztosítanak. Az elérhető műveletek a következők:

- Fordított – katasztrófa után. A reverse segít a B helynek a forrás helyének és helyének a megadásában, ahol a védett virtuális gép már él.

- Szüneteltetés – szünetelteti a kiválasztott virtuális géphez társított aktuális replikációs házirendet.

- Folytatás – szünetelteti a kiválasztott virtuális géphez társított aktuális replikációs házirendet.

- Eltávolítás – távolítsa el a kiválasztott virtuális géphez társított aktuális replikációs házirendet.

- Szinkronizálás most – a kötött szinkronizálási forrás virtuális gépről a védett virtuális gépre.

Ebben az útmutatóban a következő replikációs forgatókönyvek tartoznak:

- Virtuális gép vagy virtuális gépek egy csoportja elleni védelem.

- Végezzen el egy virtuális gépet vagy egy virtuálisgép-csoportot.

- Virtuális gép vagy virtuális gépek csoportjainak helyreállítása.

- Virtuális gép vagy virtuális gépek egy csoportjának fordított védelme.

## <a name="protect-virtual-machines"></a>Virtuális gépek elleni védelem

Jelentkezzen be a **vSphere-ügyfélbe** a forrás helyén, és nyissa meg a **HCX beépülő modult**.

:::image type="content" source="./media/disaster-recovery/hcx-vsphere.png" alt-text="HCX beállítás a vSphere" border="true":::

Adja meg a vész- **helyreállítási** területeket, és kattintson a **virtuális gépek elleni védelem**elemre.

:::image type="content" source="./media/disaster-recovery/protect-virtual-machine.png" alt-text="a virtuális gépek védelemének kiválasztása" border="true":::

A megnyíló ablakban válassza ki a forrás-és a távoli helyeket, a távoli helynek ebben az esetben az AVS Private Cloud-nak kell lennie.

:::image type="content" source="./media/disaster-recovery/protect-virtual-machines.png" alt-text="Virtuális gépek elleni védelem ablak" border="true":::

Ha szükséges, válassza ki az alapértelmezett replikációs beállításokat:

- **Tömörítés engedélyezése:** Alacsony átviteli sebességű forgatókönyvek esetén ajánlott.

- **Quiescence engedélyezése:** Szünetelteti a virtuális gépet annak biztosítására, hogy a konzisztens másolat szinkronizálva legyen a távoli hellyel.

- **Cél tárterülete:** Válassza ki a védett virtuális gép (ek) távoli adattárát. Az AVS privát felhőben ez a választás a VSAN adattár lehet.

- **Számítási tároló:** A távoli vSphere-fürt vagy-erőforráskészlet.

- **Célmappa:** A távoli célmappa, ez a beállítás nem kötelező, és ha nincs kiválasztva mappa, a virtuális gép (ek) közvetlenül a kijelölt fürt alatt lesz.

- **RPO:** Ez az érték a forrás virtuális gép és a védett virtuális gép közötti szinkronizálási időköz, amely 5 perc és 24 óra között lehet.

- **Pillanatkép időköze:** A pillanatképek közötti intervallum.

- **Pillanatképek száma:** A pillanatképek teljes száma a beállított pillanatkép-intervallumon belül.

:::image type="content" source="./media/disaster-recovery/protect-virtual-machine-options.png" alt-text="virtuális gépek beállításainak biztosítása" border="true":::

Válasszon ki egy vagy több virtuális gépet a listából, és konfigurálja a virtuális gépet szükség szerint a replikálási beállításokkal.

Alapértelmezés szerint a virtuális gépek öröklik az alapértelmezett replikációs beállításokban konfigurált globális beállítások házirendet. A kiválasztott virtuális gép minden hálózati adaptere esetében konfigurálja a távoli **hálózati port csoportot** , és válassza a **Befejezés** lehetőséget a védelmi folyamat elindításához.

:::image type="content" source="./media/disaster-recovery/network-interface-options.png" alt-text="hálózati adapter beállításai" border="true":::

Ahogy az alábbi ábrán is látható, az egyes kiválasztott virtuális gépek folyamatát is figyelheti ugyanabban a vész-helyreállítási területen.

:::image type="content" source="./media/disaster-recovery/protect-monitor-progress.png" alt-text="a védelem előrehaladásának figyelése" border="true":::

A virtuális gép védelme után a **Pillanatképek** lapon láthatja a különböző pillanatképeket.

:::image type="content" source="./media/disaster-recovery/list-of-snapshots.png" alt-text="Pillanatképek listája" border="true":::

A sárga háromszög azt jelenti, hogy a pillanatképek és a virtuális nem tesztelt teszt-helyreállítási műveletben.

Fontos különbségek vannak a kikapcsolt virtuális gépek és az azt bekapcsoló virtuális gépek között.
Az előző képernyőképen egy virtuális gépen futó virtuális gép szinkronizálási folyamata látható. Elindította a szinkronizálási folyamatot, amíg befejeződik az első pillanatkép, amely a virtuális gép teljes másolata, majd befejezi a következőket a beállított intervallumban.

A kikapcsolt virtuális gépek esetében szinkronizál egy másolatot, majd a virtuális gép inaktívként jelenik meg, és a védelmi művelet befejezve állapotba kerül.

Ha a virtuális gép be van kapcsolva, a rendszer elindítja a szinkronizálási folyamatot a távoli helyre.

## <a name="complete-a-test-recover-of-virtual-machines"></a>Virtuális gépek teszt-helyreállításának befejezése

Jelentkezzen be a **vSphere-ügyfélbe** a távoli helyen, amely az AVS Private Cloud. A **HCX beépülő modulban**, a vész-helyreállítási területen válassza ki a függőleges ellipsziseket bármelyik virtuális gépen az operatív menü megjelenítéséhez. Válassza a **teszt virtuális gép helyreállítása**lehetőséget.

:::image type="content" source="./media/disaster-recovery/test-recover-virtual-machine.png" alt-text="Válassza ki a virtuális gép helyreállítása" border="true":::

Az új ablakban válassza ki a teszt beállításait. Válassza ki a virtuális gép különböző állapotának teszteléséhez használni kívánt pillanatképet.

:::image type="content" source="./media/disaster-recovery/choose-snapshot.png" alt-text="Válasszon ki egy pillanatképet, és kattintson a teszt gombra." border="true":::

A **tesztelésre**való kattintás után elindul a helyreállítási művelet.

A teszt-helyreállítási művelet befejezésekor az új virtuális gép bejelölhető az AVS Private Cloud vCenter.

:::image type="content" source="./media/disaster-recovery/verify-test-recovery.png" alt-text="helyreállítási művelet keresése" border="true":::

Végül, miután végzett a teszteléssel a virtuális gépen vagy bármely, a rendszeren futó alkalmazáson, tisztítással törölheti a tesztelési példányt.

:::image type="content" source="./media/disaster-recovery/cleanup-test-instance.png" alt-text="tesztelési példány törlése" border="true":::

## <a name="recover-virtual-machines"></a>Virtuális gépek helyreállítása

Jelentkezzen be a **vSphere-ügyfélbe** a távoli helyen, amely az AVS Private Cloud, és hozzáfér a **HCX beépülő modulhoz**.

A helyreállítási forgatókönyvhöz az ebben a példában használt virtuális gépek egy csoportja.

Válassza ki a listából a helyreállítani kívánt virtuális gépet, nyissa meg a **műveletek** menüt, és válassza a virtuális **gépek helyreállítása**lehetőséget.

:::image type="content" source="./media/disaster-recovery/recover-virtual-machines.png" alt-text="virtuális gépek helyreállítása" border="true":::

Konfigurálja az egyes példányok helyreállítási beállításait, majd kattintson **a helyreállítás** elemre a helyreállítási művelet elindításához.

:::image type="content" source="./media/disaster-recovery/recover-virtual-machines-confirm.png" alt-text="virtuális gépek helyreállításának megerősítése" border="true":::

A helyreállítási művelet befejezése után az új virtuális gépek megjelennek a távoli vCenter Server leltárban.

## <a name="complete-a-reverse-replication-on-virtual-machines"></a>Visszirányú replikálás végrehajtása a virtuális gépeken

Jelentkezzen be a **vSphere-ügyfélre** az AVS Private-felhőben, és nyissa meg a **HCX beépülő modult**.
Szükség van arra, hogy a forrás helyén lévő eredeti virtuális gépek ki legyenek kapcsolva a visszirányú replikálás megkezdése előtt. A művelet meghiúsul, ha a virtuális gépek nincsenek kikapcsolva.

Válassza ki azokat a virtuális gépeket, amelyeket vissza szeretne állítani a forrás helyén a listából, nyissa meg a **műveletek** menüt, és válassza a **fordított**lehetőséget. Az előugró ablakban kattintson a **vissza** gombra a replikálás elindításához.

:::image type="content" source="./media/disaster-recovery/reverse-operation-virtual-machines.png" alt-text="A védelmi műveletek területen válassza a fordított művelet lehetőséget." border="true":::

A replikációt az egyes virtuális gépek részletek szakaszában lehet figyelni.

:::image type="content" source="./media/disaster-recovery/review-reverse-operation.png" alt-text="a fordított művelet eredményének áttekintése" border="true":::

## <a name="disaster-recovery-plan-automation"></a>Vész-helyreállítási terv automatizálása

A VMware HCX jelenleg nem rendelkezik beépített mechanizmussal a vész-helyreállítási terv létrehozásához és automatizálásához. Ez a funkció nem létezik a HCX. Azonban REST API-kat is biztosít, beleértve a vész-helyreállítási művelethez szükséges API-kat is.

Az API-specifikáció a HCX-kezelőben az URL-címen érhető el.

Ezek az API-k a vész-helyreállítás következő műveleteit tárgyalják.

- Védelem

- Helyreállítás

- Teszt helyreállítása

- Tervezett helyreállítás

- Reverse

- Lekérdezés

- Teszt tisztítása

- Szünet

- Folytatás

- Védelem eltávolítása

- Újrakonfigurálás

Az alábbi példa egy helyreállítási művelet hasznos adatait mutatja be a JSON-ban.

```json
[

    {

        "replicationId": "string",

        "needPowerOn": true,

        "instanceId": "string",

        "source": {

            "endpointType": "string",

            "endpointId": "string",

            "endpointName": "string",

            "resourceType": "string",

            "resourceId": "string",

            "resourceName": "string"

        },

        "destination": {

            "endpointType": "string",

            "endpointId": "string",

            "endpointName": "string",

            "resourceType": "string",

            "resourceId": "string",

            "resourceName": "string"

        },

        "placement": [

            {

                "containerType": "string",

                "containerId": "string"

            }

        ],

        "resourceId": "string",

        "forcePowerOff": true,

        "isTest": true,

        "forcePowerOffAfterTimeout": true,

        "isPlanned": true

    }

]
```

Ezen API-k használatával az ügyfél létrehozhat egy egyéni mechanizmust a vész-helyreállítási terv létrehozásának és végrehajtásának automatizálására.
