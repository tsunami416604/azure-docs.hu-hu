---
title: Állítsa be a készülék az Azure Migrálása kiszolgáló értékelési és a Migrálás a VMware virtuális gépekhez |} A Microsoft Docs
description: Ismerteti, hogyan állítható be a felderítés, értékelési és ügynök nélküli migrálást VMware virtuális gépek Azure Migrate értékelés /-áttelepítés használatával telepíthetőek.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/08/2019
ms.author: raynew
ms.openlocfilehash: fe190381df346278e75a3e6fd9876b80c33bd86b
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811725"
---
# <a name="set-up-an-appliance-for-vmware-vms"></a>Egy készülék beállítása VMware virtuális gépekhez

Ez a cikk ismerteti, hogyan állítható be az Azure Migrate berendezés, ha VMware virtuális gépek értékelése az Azure Migrate Server Assessment eszközzel, vagy a VMware virtuális gépek migrálása az Azure-bA az ügynök nélküli migrálást az Azure Migrate Server áttelepítési eszköz használata.

A VMware virtuális gép berendezés egy egyszerűsített berendezés által Azure Migrate Server Assessment és a Migrálás használt tegye a következőket:

- Fedezze fel a helyszíni VMware virtuális gépeket.
- Metaadatok és a teljesítmény az adatok elküldése a felderített virtuális gépek az Azure Migrálása kiszolgáló értékelési és a Migrálás.

[További](migrate-appliance.md) tudnivalók az Azure Migrate berendezés.


## <a name="appliance-deployment-steps"></a>Berendezés üzembe helyezés lépései

Beállítása a készülék meg:
- Töltse le az OVA sablon fájlt, és importálja a vCenter Serverhez.
- A berendezés létrehozásához, és ellenőrizze, hogy képes csatlakozni az Azure Migrate Server Assessment. 
- Konfigurálja a berendezést először, és regisztrálja az Azure Migrate-projektben.

## <a name="download-the-ova-template"></a>Az OVA-sablon letöltése

1. A **áttelepítési célok** > **kiszolgálók** > **Azure Migrate: Server Assessment**, kattintson a **felderítési**.
2. A **gépek felderítése** > **virtualizáltak a gépek?** , kattintson a **Igen, a VMWare vSphere hipervizor**.
3. Kattintson a **letöltése** letöltéséhez a. Sablon OVA-fájl.



### <a name="verify-security"></a>Biztonsági ellenőrzése

Ellenőrizze, hogy az OVA-fájl biztonságos, az üzembe helyezés előtt.

1. A gépen, amelyre a fájlt letöltötte, nyisson meg egy rendszergazdai parancsablakot.
2. Futtassa a következő parancsot az OVA hozza létre a kivonat:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Gyakorlati példa: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. A berendezés 1.0.0.5 esetén a létrehozott kivonatnak egyeznie kell az ezeket a beállításokat. 

  **Algoritmus** | **Kivonat értéke**
  --- | ---
  MD5 | ddfdf21c64af02a222ed517ce300c977


## <a name="create-the-appliance-vm"></a>A berendezés virtuális gép létrehozása

Importálja a letöltött fájlt, és hozzon létre egy virtuális Gépet.

1. A vSphere Client-konzolon kattintson a **Fájl** > **OVF-sablon telepítése** elemre.
2. Az OVF-sablon üzembe helyezése varázsló > **forrás**, adja meg az OVA-fájl helyét.
3. A **neve** és **hely**, adjon meg egy rövid nevet a virtuális gép számára. Válassza ki a készlet objektum, amelyben a virtuális gép fog üzemeltetni.
5. A **gazdagéphez/fürthöz**, adja meg a gazdagépet, vagy a fürtöt, amely a virtuális gép fog futni.
6. A **tárolási**, adja meg a célhelyet a virtuális gép számára.
7. A **Disk Format** (Lemezformátum) mezőben adja meg a lemez típusát és méretét.
8. A **Hálózatleképezés**, adja meg a hálózatot, amelyhez a virtuális gépek csatlakozni fognak. Metaadatokat küldhet az Azure Migrate Server Assessment internetkapcsolattal kell rendelkeznie a a hálózaton.
9. Tekintse át és hagyja jóvá a beállításokat, majd kattintson a **Finish** (Befejezés) gombra.


### <a name="verify-appliance-access-to-azure"></a>Az Azure-bA készülék-hozzáférés ellenőrzése

Győződjön meg arról, hogy a berendezés virtuális gép kapcsolódik- [Azure URL-címeinek](migrate-support-matrix-vmware.md#assessment-url-access-requirements).


## <a name="configure-the-appliance"></a>Konfigurálja a berendezést

A berendezés beállítása az első alkalommal.

1. A vSphere Client-konzolon kattintson a jobb gombbal a virtuális gépre, majd kattintson az **Open Console** (Konzol megnyitása) elemre.
2. Adja meg a berendezés a nyelv, az időzóna és a jelszót.
3. Nyisson meg egy böngészőt, minden olyan gépen, amely a virtuális gép csatlakozhat, és nyissa meg a webalkalmazás URL-címét a készülék: **https://*berendezés neve vagy IP-cím*: 44368**.

   Azt is megteheti megnyithatja az alkalmazás a készülék asztalról az alkalmazás parancsikonjára kattintva.
4. A webalkalmazásban > **Előfeltételek beállítása**, tegye a következőket:
    - **Licenc**: Fogadja el a licencfeltételeket, és olvassa el a külső szolgáltatóval kapcsolatos információkat.
    - **Kapcsolat**: Az alkalmazás ellenőrzi, hogy a virtuális gép rendelkezik-e internet-hozzáféréssel. Ha a virtuális gép egy proxyt használja:
        - Kattintson a **proxybeállítások**, és adja meg a proxykiszolgáló címét és figyelőportját, a képernyőn http://ProxyIPAddress vagy http://ProxyFQDN.
        - Adja meg a hitelesítő adatokat, ha a proxykiszolgáló hitelesítést igényel.
        - Csak a HTTP-proxyk használata támogatott.
    - **Szinkronizálási idő**: Idő ellenőrizve. Lehet, hogy az idő, a készüléken felderítés működéséhez internet időt szinkronizálva.
    - **Telepítse a frissítéseket**: Az Azure Migrate ellenőrzi, hogy telepítve vannak-e a készülék legújabb frissítéseit.
    - **Telepítse a VDDK**: Az Azure Migrate ellenőrzi, hogy telepítve van-e a VMWare vSphere virtuális lemez Development Kit (VDDK).
        - Azure Migrates a VDDK használja a gépek replikálása az Azure-ba való migráláskor.
        - Töltse le a VDDK 6.7 a VMware-ből, és csomagolja ki a letöltött zip tartalmát a megadott helyen, a készüléken.

## <a name="register-the-appliance-with-azure-migrate"></a>A berendezés regisztráljon az Azure Migrate

1. Kattintson a **bejelentkezés**. Ha nem jelenik meg, győződjön meg arról, akkor le lett tiltva az előugró ablakok a böngészőben.
2. Az új lapon jelentkezzen be Azure hitelesítő adatait. 
    - Jelentkezzen be a felhasználónevét és jelszavát.
    - Jelentkezzen be a PIN-kód nem támogatott.
3. Miután sikeresen bejelentkezett, lépjen vissza a webalkalmazás.
2. Válassza ki az előfizetést, amelyben az Azure Migrate-projekt létrehozása. Ezután válassza ki a projektet.
3. Adjon meg egy nevet a berendezéshez. A névnek alfanumerikus karakterek és 14 karakter vagy kevesebb kell lennie.
4. Kattintson a **regisztrálása**.


## <a name="start-continuous-discovery"></a>Folyamatos felderítés indítása

Most a készülék vCenter-kiszolgálóhoz csatlakozhat, és indítsa el a virtuális gépek felderítésének. 

1. A **adja meg a vCenter Server adatait**, adja meg a nevét (FQDN) vagy a vCenter-kiszolgáló IP-címét. Hagyja bejelölve az alapértelmezett port, vagy adjon meg egy egyéni portot, amelyen a vCenter-kiszolgáló figyeli.
2. A **felhasználónév** és **jelszó**, adja meg a csak olvasható fiók hitelesítő adatait, amelynek használatával a berendezést a vCenter-kiszolgáló a virtuális gépek felderítéséhez. Győződjön meg arról, hogy a fiók rendelkezik-e a [szükséges engedélyek a felderítéshez](migrate-support-matrix-vmware.md#assessment-vcenter-server-permissions).
3. Kattintson a **-kapcsolat ellenőrzése** , győződjön meg arról, hogy a berendezés vCenter-kiszolgálóhoz csatlakozhat.
4. A kapcsolat létrejötte után kattintson a **mentéséhez és a felderítés megkezdése**.


Ez elindítja a felderítést. Megjelenik a portálon a felderített virtuális gépek metaadatait hozzávetőlegesen 15 percet vesz igénybe. 


## <a name="next-steps"></a>További lépések

Tekintse át a számára oktatóanyagokkal [VMware értékelés](tutorial-assess-vmware.md) és [ügynök nélküli migrálást](tutorial-migrate-vmware.md).
