---
title: Az Azure disk encryption (ADE) esetében engedélyezve van a replikáció virtuális gépek konfigurálása az Azure Site Recovery |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan ADE virtuális gépek esetén egy Azure-régióból a másikba Site recoveryvel a replikáció konfigurálása.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 09/28/2018
ms.author: sutalasi
ms.openlocfilehash: 5d1beb124bbb857d13aecad7bf0cef493d42dac5
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2018
ms.locfileid: "48043277"
---
# <a name="replicate-azure-disk-encryption-ade-enabled-virtual-machines-to-another-azure-region"></a>Az Azure disk encryption (ADE) engedélyezve van a virtuális gépek replikálása másik Azure-régióba

Ez a cikk ismerteti, hogyan engedélyezheti az Azure disk encryption (ADE) engedélyezve replikációját virtuális gépek Azure-régióból egy másikba.

>[!NOTE]
>Csak a Windows operációs rendszert futtató Azure virtuális és [engedélyezett a titkosítás az Azure AD-alkalmazás](https://aka.ms/ade-aad-app) Azure Site Recovery által jelenleg támogatott.
>

## <a name="required-user-permissions"></a>Szükséges felhasználói engedélyek

Ahhoz, hogy a portálról ADE virtuális gépek replikálását, a felhasználóknak rendelkezniük kell ahhoz az alábbi engedélyeket.
- Key vault-engedélyek
    - lista
    - Létrehozás
    - Lekérés

-   A Key vault titkos kód engedélyei
    - Lista
    - Létrehozás
    - Lekérés

- A Key vault Kulcsengedélyek (csak akkor, ha a virtuális gépek kulcstitkosítási kulcs segítségével titkosíthatja a lemezt titkosítási kulcsok kötelező)
    - Lista
    - Lekérés
    - Létrehozás
    - Titkosítás
    - Visszafejtés

Ellenőrizheti, hogy a key vault erőforrás-portálon, és vegye fel a szükséges engedélyekkel a felhasználó kezelheti az engedélyeket.

![keyvaultpermissions](./media/azure-to-azure-how-to-enable-replication-ade-vms/keyvaultpermissions.png)

Vészhelyreállítás (DR) engedélyezése a felhasználó nem rendelkezik a szükséges engedélyekkel, másolja ki a kulcsokat, ha az alábbi szkriptet adható meg a biztonsági rendszergazda a megfelelő engedélyekkel. a titkosítási titkos kódok és kulcsok átmásolása a célrégióban.

>[!NOTE]
>Ahhoz, hogy a portálról ADE virtuális gép replikációját, legalább "List" engedélyek szükségesek a kulcstartók, kulcsok és titkok
>

## <a name="copy-ade-keys-to-dr-region-using-powershell-script"></a>PowerShell-szkript használatával Vészhelyreállítási régióban ADE kulcsok másolása

1. Nyissa meg a "CopyKeys" nyers szkriptkódot egy böngészőablakban kattintva [ezt a hivatkozást](https://aka.ms/ade-asr-copy-keys-code).
2. Másolja a fájlba, és nevezze el "Másolás-keys.ps1".
2. Nyissa meg a Windows PowerShell alkalmazást, és nyissa meg azt a mappát, ahol a fájl létezik-e.
3. Indítsa el a "Másolás-keys.ps1"
4. Adja meg az Azure bejelentkezési hitelesítő adatait.
5. Válassza ki a **Azure-előfizetés** a virtuális gépeket.
6. Várjon, amíg az erőforráscsoportok betöltéséhez, majd kattintson a **erőforráscsoport** a virtuális gépeket.
7. Válassza ki a virtuális gépek, virtuális gépek jelennek meg a listából. Csak virtuális gépek az Azure disk encryption ellátott jelennek meg a listában.
8. Válassza ki a **célhelye**.
9. **Lemez titkosítása kulcstartók**: alapértelmezés szerint a Azure Site Recovery létrehoz egy új kulcstartót a célrégióban a forrás virtuális gépek lemeztitkosítási kulcsok alapján "asr" utótaggal rendelkező nevű. Kulcstartó létrehozása az Azure Site Recovery már létezik, a rendszer újra. Egy másik kulcstartót kiválaszthatja a listából, ha szükséges.
10. **A kulcstartók titkosító kulcs**: alapértelmezés szerint a Azure Site Recovery létrehoz egy új kulcstartót a célrégióban a forrás virtuális gépek kulcstitkosítási kulcsokon alapuló "asr" utótaggal rendelkező nevű. Kulcstartó létrehozása az Azure Site Recovery már létezik, a rendszer újra. Egy másik kulcstartót kiválaszthatja a listából, ha szükséges.

## <a name="enable-replication"></a>A replikáció engedélyezése

Ez az eljárás feltételezi, hogy az elsődleges Azure-régió Kelet-Ázsia, pedig a másodlagos régióba Délkelet-Ázsia.

1. Kattintson a tárolóban **+ replikálás**.
2. Vegye figyelembe a következő mezőket:
    - **Forrás**: A forrás virtuális gépet, amely ebben az esetben pontját **Azure**.
    - **Forrás helye**: az Azure-régió, ahonnan csak szeretné a virtuális gép védelméhez. Ezen az ábrán a forráshely Kelet-Ázsia
    - **Üzemi modell**: Azure üzembehelyezési modell forrásgépek közül.
    - **Forrás-előfizetés**: az előfizetés, a forrás virtuális gépeket, amelyekhez tartoznak. Ez bármelyik előfizetés lehet azon Azure Active Directory-bérlőn belül, ahol a már meglévő Recovery Services-tárolója van.
    - **Erőforráscsoport**: az erőforráscsoport, a forrás virtuális gépeket, amelyekhez tartoznak. A kiválasztott erőforráscsoportba tartozó összes virtuális gép jelennek meg a következő lépésben védelemre.

3. A **virtuális gépek > Válassza ki a virtuális gépek**kattintson, és válassza a minden replikálni kívánt virtuális Géphez. Csak olyan gépeket választhat, amelyeken használható a replikáció funkció. Kattintson a **OK**.

4. A **beállítások**, igény szerint konfigurálható cél webhely beállításai:

    - **Célhely**: A hely, ahol a forrás virtuális gép fognak replikálódni. Függően a kijelölt gépekhez helyét, a Site Recovery biztosít Önnek a megfelelő régiók listáját. Azt javasoljuk, hogy őrizze meg a célhelyen ugyanaz, mint a Recovery Services-tár helye.
    - **Célelőfizetés**: A vészhelyreállításhoz használt célelőfizetés. Alapértelmezés szerint a célelőfizetés megegyezik a forrás-előfizetéssel.
    - **Céloldali erőforráscsoport**: az erőforráscsoport, ahol az összes a replikált virtuális gépek tartoznak. Alapértelmezés szerint az Azure Site Recovery hoz létre egy új erőforráscsoportot a célrégióban az "asr" utótaggal rendelkező neve. Erőforráscsoport létrehozása az Azure Site Recovery már létezik, a rendszer újra. Azt is beállíthatja az alábbi szakaszban látható módon szabhatja testre. A céloldali erőforráscsoport helye lehet bármelyik Azure-régióban, amelyben a forrás virtuális gépeket üzemeltetett régiót kivéve.
    - **Cél virtuális hálózat**: alapértelmezés szerint a Site Recovery nevű hoz létre egy új virtuális hálózat a célrégióban az "asr" utótaggal rendelkező. Ez a Forráshálózat leképezve, és minden jövőbeli védelemhez használt. [További](site-recovery-network-mapping-azure-to-azure.md) információk a hálózatleképezésről.
    - **Cél tárfiókok (Ha a forrás virtuális gép nem használ felügyelt lemezeket)**: alapértelmezés szerint a Site Recovery létrehoz egy új céltárfiók, a forrás virtuális gép tárkonfigurációt mimicking. Storage-fiók már létezik, a rendszer újra.
    - **Replikált felügyelt lemezek (Ha a forrásoldali virtuális gép felügyelt lemezeket használ)**: Site Recovery új replika felügyelt lemezeket hoz létre a célrégióban a forrás virtuális gép felügyelt lemezeinek azokkal az azonos tártípusban (Standard vagy prémium) tükrözik, mivel a forrásoldali virtuális gép felügyelt lemezt.
    - **Gyorsítótár tárfiókjai**: Site recoverynek a extra storage-fiókkal, amelynek neve a gyorsítótárban a forrásrégióban. Történik a forrásoldali virtuális gép az összes módosítást a nyomon követett és a gyorsítótárfiókba azokat a célhelyre történő replikálása előtt küldött.
    - **A rendelkezésre állási csoport**: alapértelmezés szerint az Azure Site Recovery létrehoz egy új rendelkezésre állási csoportot a célrégióban az "asr" utótaggal rendelkező neve. Rendelkezésre állási csoport létrehozása az Azure Site Recovery már létezik, a rendszer újra.
    - **Lemez titkosítása kulcstartók**: alapértelmezés szerint a Azure Site Recovery létrehoz egy új kulcstartót a célrégióban a forrás virtuális gépek lemeztitkosítási kulcsok alapján "asr" utótaggal rendelkező nevű. Kulcstartó létrehozása az Azure Site Recovery már létezik, a rendszer újra.
    - **A kulcstartók titkosító kulcs**: alapértelmezés szerint a Azure Site Recovery létrehoz egy új kulcstartót a célrégióban a forrás virtuális gépek kulcstitkosítási kulcsokon alapuló "asr" utótaggal rendelkező nevű. Kulcstartó létrehozása az Azure Site Recovery már létezik, a rendszer újra.
    - **Replikációs házirend**: azt határozza meg a helyreállítási pont megőrzési előzményekkel és alkalmazáskonzisztens pillanatkép készítésének gyakorisága beállításait. Alapértelmezés szerint az Azure Site Recovery egy új replikációs házirendet hoz beállítás alapértelmezett 24 órányi a helyreállítási pont megőrzése és az "60 percben az alkalmazáskonzisztens pillanatkép gyakorisága.



## <a name="customize-target-resources"></a>Célerőforrások testreszabása

A Site Recovery által használt alapértelmezett célbeállítások módosíthatja.


1. Kattintson a **testreszabása:** melletti "Célként megadott előfizetés" az alapértelmezett célként megadott előfizetés módosításához. Válassza ki az előfizetést az Azure Active Directory (AAD) ugyanabban a bérlőben elérhető előfizetések listájából.

2. Kattintson a **testreszabása:** melletti "erőforráscsoportot, tárolási, hálózati és rendelkezésre állási állítja be, módosíthatja a alatt alapbeállításait:
    - A **céloldali erőforráscsoport**, válassza ki az erőforráscsoportot a listából a célként megadott helyen az előfizetés összes erőforráscsoportot.
    - A **cél virtuális hálózattal**, válassza ki a hálózatot a virtuális hálózat a célhelyen egy listából.
    - A **rendelkezésre állási csoport**, adhat hozzá rendelkezésre állási csoport beállításait a virtuális géphez, ha egy rendelkezésre állási csoportot a forrásrégióban részei.
    - A **cél tárfiókok**, válassza ki a használni kívánt fiókot.


2. Kattintson a **testreszabása:** melletti "Titkosítási beállítások" módosítása az alapértelmezett beállításokat alább:
    - A **cél lemez titkosítása a key vault**, válassza ki a cél lemez titkosítása a key vault a célként megadott helyen az előfizetés összes kulcstárolójának listájából.
  - A **cél kulcs titkosítása a key vault**, válassza ki a cél kulcs titkosítása a key vault a célként megadott helyen az előfizetés összes kulcstárolójának listájából.

3. Kattintson a **célként megadott erőforrás létrehozása** > **replikáció engedélyezése**.
4. Után a replikáció engedélyezve vannak a virtuális gépek, virtuális gép állapota alapján állapotát ellenőrizheti **replikált elemek**

>[!NOTE]
>Kezdeti replikálás során a állapota eltarthat egy ideig, frissítése, anélkül, hogy folyamatban van. Kattintson a **frissítése** gombra, a legújabb állapotának beolvasása.
>

## <a name="update-target-vm-encryption-settings"></a>Cél virtuális gép titkosítási beállításainak frissítése
Az az alábbi forgatókönyvek esetén meg kell adni a cél virtuális gép titkosítási beállításainak frissítése.
  - Site recovery-replikációja a virtuális gépen engedélyezve van, és engedélyezve van az Azure Disk Encryption (ADE) a forrásoldali virtuális gép egy későbbi időpontban
  - Engedélyezve van a virtuális gép Site recovery-replikációja, és a egy későbbi időpontban a lemezének titkosítási kulcsa és/vagy a kulcsalapú titkosítás kulcsa a forrásoldali virtuális gép megváltozott

Használható [a parancsfájl](#copy-ade-keys-to-dr-region-using-powershell-script) célrégió másolja a titkosítási kulcsokat, és frissítse az titkosítási beállításainak **a Recovery services-tár -> replikált elem -> Tulajdonságok -> Számítás és hálózat.**

![frissítés-ade-beállítások](./media/azure-to-azure-how-to-enable-replication-ade-vms/update-ade-settings.png)

## <a name="next-steps"></a>További lépések

[További](site-recovery-test-failover-to-azure.md) feladatátvételi teszt futtatásáról.
