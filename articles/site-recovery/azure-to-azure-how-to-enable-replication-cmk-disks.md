---
title: A titkosított Azure-virtuális gépek replikációjának engedélyezése az Azure Site Recovery szolgáltatásban
description: Ez a cikk bemutatja, hogyan konfigurálhatja a virtuális gépek replikációját az ügyfél által felügyelt kulcs (CMK) által engedélyezett lemezek egyik Azure-régióból a másikba a Site Recovery használatával.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 01/10/2020
ms.author: mayg
ms.openlocfilehash: 367f29237a3f2a634f209026df47b0cbd6ffc97c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75897961"
---
# <a name="replicate-machines-with-customer-managed-keys-cmk-enabled-disks"></a>Gépek replikálása ügyféláltal felügyelt kulcsokkal (CMK) engedélyezett lemezekkel

Ez a cikk ismerteti, hogyan replikálható az Azure virtuális gépek ügyfél által felügyelt kulcsok (CMK) engedélyezett felügyelt lemezek, egyik Azure-régióból a másikba.

## <a name="prerequisite"></a>Előfeltétel
A cmk-kompatibilis felügyelt lemezekkel rendelkező virtuális gépek replikációjának engedélyezése előtt létre kell hoznia a cél-előfizetés célrégiójában a lemeztitkosítási készlet(eke)t.

## <a name="enable-replication"></a>A replikáció engedélyezése

Ebben a példában az elsődleges Azure-régió Kelet-Ázsia, a másodlagos régió pedig Délkelet-Ázsia.

1. A tárolóban válassza a **+Replicate**lehetőséget.
2. Vegye figyelembe a következő mezőket.
    - **Forrás**: A virtuális gépek származási helye, amely ebben az esetben az **Azure.**
    - **Forrás helye:** Az Azure-régió, ahol meg szeretné védeni a virtuális gépeket. Ebben a példában a forráshely "Kelet-Ázsia".
    - **Üzembe helyezési modell:** A forrásgépek Azure üzembe helyezési modellje.
    - **Forrás-előfizetés**: Az az előfizetés, amelyhez a virtuális forrásgépek tartoznak. Bármilyen előfizetés lehet, amely ugyanabban az Azure Active Directory-bérlőben található, mint a helyreállítási szolgáltatások tárolója.
    - **Erőforráscsoport:** Az az erőforráscsoport, amelyhez a forrásvirtuális gépek tartoznak. A kijelölt erőforráscsoportban lévő összes virtuális gép a következő lépésben védelemre van felsorolva.

3. Virtuális **gépek** > válassza ki a**virtuális gépek,** válassza ki az egyes virtuális gépek, amelyek replikálni kívánt. Csak olyan gépeket választhat, amelyeken használható a replikáció funkció. Ezután válassza **az OK gombot.**

4. A **Beállítások párbeszédpanelen**a következő célhely-beállításokat állíthatja be.

    - **Célhely:** Az a hely, ahol a forrásvirtuális gép adatai replikálódnak. A Site Recovery a kiválasztott gép helye alapján tartalmazza a megfelelő célterületek listáját. Azt javasoljuk, hogy használja ugyanazt a helyet, mint a Recovery Services tároló helyét.
    - **Cél-előfizetés:** A vész-helyreállítási használt cél-előfizetés. Alapértelmezés szerint a cél-előfizetés megegyezik a forrás-előfizetés.
    - **Célerőforrás-csoport:** Az az erőforráscsoport, amelyhez az összes replikált virtuális gép tartozik. Alapértelmezés szerint a Site Recovery új erőforráscsoportot hoz létre a célrégióban. A név `asr` megkapja az utótagot. Ha már létezik olyan erőforráscsoport, amelyet az Azure Site Recovery hozott létre, újra fel használja. Testre is szabhatja, ahogy az a következő szakaszban is látható. A célerőforrás-csoport helye bármely Azure-régió lehet, kivéve azt a régiót, ahol a forrás virtuális gépek vannak tárolva.
    - **Virtuális hálózat célzása**: Alapértelmezés szerint a Site Recovery új virtuális hálózatot hoz létre a célrégióban. A név `asr` megkapja az utótagot. A forráshálózathoz van rendelve, és bármilyen jövőbeli védelemre használható. [További információ](site-recovery-network-mapping-azure-to-azure.md) a hálózati hozzárendelésről.
    - **Céltárfiókok (ha a forrás virtuális gép nem használ felügyelt lemezeket) :** Alapértelmezés szerint a Site Recovery új céltárfiókot hoz létre a forrás virtuálisgép-tároló konfigurációjának utánzásával. Ha egy tárfiók már létezik, a rendszer újra felhasználja.
    - **Replika felügyelt lemezek (ha a forrás virtuális gép felügyelt lemezeket használ)**: Site Recovery új replika felügyelt lemezek a célrégióban, hogy tükrözze a forrás virtuális gép felügyelt lemezek azonos tárolótípusú (standard vagy prémium) a forrás virtuális gép felügyelt lemezek.
    - **Gyorsítótár-tároló fiókok:** Site Recovery szüksége van egy extra tárfiók nevű *gyorsítótár-tároló* a forrásrégióban. A forrás virtuális gépek összes változását nyomon követi nyomon, és elküldi a gyorsítótár-tárfiókba. Ezután replikálódiknak a célhelyre.
    - **Rendelkezésre állási készlet:** Alapértelmezés szerint a Site Recovery új rendelkezésre állási készletet hoz létre a célrégióban. A név `asr` utótaggal rendelkezik. Ha a Site Recovery által létrehozott rendelkezésre állási csoport már létezik, a program újra felhasználja azt.
    - **Lemeztitkosítási készletek (DES):** A Site Recovery-nek szüksége van a lemeztitkosítási készlet(ek)re a replika- és célkezelt lemezekhez. A replikáció engedélyezése előtt előzetesen létre kell hoznia a DES-t a cél-előfizetésben és a célrégióban. Alapértelmezés szerint a DES nincs kijelölve. A "Testreszabás" gombra kell kattintania a FORRÁSLEMEZENKÉNTi DES kiválasztásához.
    - **Replikációs házirend**: A helyreállítási pontok megőrzési előzményeiés az alkalmazáskonzisztens pillanatkép gyakoriságának beállításait határozza meg. Alapértelmezés szerint a Site Recovery új replikációs házirendet hoz létre, amelynek alapértelmezett beállításai *24 óra* a helyreállítási pontok megőrzésére, és *60 perc* az alkalmazáskonzisztens pillanatképek gyakoriságára.

    ![Replikáció engedélyezése a cmk-kompatibilis lemezekkel rendelkező gépeken](./media/azure-to-azure-how-to-enable-replication-cmk-disks/cmk-enable-dr.png)

## <a name="customize-target-resources"></a>Célerőforrások testreszabása

A Site Recovery alapértelmezett célbeállításainak módosításához kövesse az alábbi lépéseket.

1. Válassza a **Testreszabás** lehetőséget a "Cél-előfizetés" elem mellett az alapértelmezett cél-előfizetés módosításához. Válassza ki az előfizetést az Azure AD-bérlőben elérhető előfizetések listájából.

2. Válassza az "Erőforráscsoport, hálózat, tárhely és rendelkezésre állás" elem melletti **Testreszabás** lehetőséget a következő alapértelmezett beállítások módosításához:
    - A **Cél erőforráscsoport**csoport ban válassza ki az erőforráscsoportot az erőforráscsoportok listájából az előfizetés célhelyén.
    - A **Cél virtuális hálózat**, válassza ki a hálózatot a listában a virtuális hálózatok a célhelyen.
    - Az **elérhetőségi készlet**hez hozzáadhatja a rendelkezésre állási beállításokat a virtuális géphez, ha azok a forrásrégióban lévő rendelkezésre állási csoport részét képezik.
    - A **Céltár-fiókok esetében**válassza ki a használni kívánt fiókot.

3. Válassza a **Testreszabás** lehetőséget a "Tárolási titkosítási beállítások" elem mellett, ha minden ügyfél által felügyelt kulcs (CMK) engedélyezett forrásfelügyelt lemezhez ki szeretné választani a cél DES-t. A kiválasztás időpontjában azt is láthatja, hogy a DES melyik célkulcstartóhoz van társítva.

4. Válassza **a Célerőforrás** > **létrehozása replikáció engedélyezése**lehetőséget.
5. Miután a virtuális gépek engedélyezve vannak a replikációhoz, ellenőrizheti a virtuális gépek állapotát a **Replikált elemek csoportban.**

![Replikáció engedélyezése a cmk-kompatibilis lemezekkel rendelkező gépeken](./media/azure-to-azure-how-to-enable-replication-cmk-disks/cmk-customize-target-disk-properties.png)

>[!NOTE]
>A kezdeti replikáció során az állapot frissítése némi időt vehet igénybe, látszólagos előrehaladás nélkül. A legújabb állapot bekerüléséhez kattintson a **Frissítés** gombra.

## <a name="faqs"></a>Gyakori kérdések

* Engedélyeztem a CMK-t egy meglévő replikált elemen, hogyan biztosíthatom, hogy a CMK a célrégióra is vonatkozik?

    Megtudhatja a replikátmen kezelt lemez nevét (amelyet az Azure Site Recovery a célrégióban hozott létre), és des-t csatolhat ehhez a replikalemezhez. Azonban nem fogja látni a DES részleteket a Lemezek panelen, miután csatlakoztatta. Másik lehetőségként dönthet úgy, hogy letiltja a virtuális gép replikációját, és újra engedélyezi azt. Ez biztosítja, hogy a DES és a key vault részleteit a disks panelen a replikált elem.

* Új CMK-kompatibilis lemezt adtam a replikált elemhez. Hogyan replikálhatom ezt a lemezt az Azure Site Recovery szolgáltatással?

    Nem támogatott új CMK-kompatibilis lemez hozzáadása egy meglévő replikált elemhez. Tiltsa le a replikációt, és engedélyezze újra a replikációt a virtuális gépen.

