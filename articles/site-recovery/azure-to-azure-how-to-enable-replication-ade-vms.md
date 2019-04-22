---
title: Konfigurálja az Azure Disk Encryption-kompatibilis virtuális gépek replikálása az Azure Site Recovery |} A Microsoft Docs
description: Ez a cikk ismerteti az Azure Disk Encryption-kompatibilis virtuális gépek egy Azure-régióból a másikba replikáció konfigurálását a Site Recovery használatával.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/08/2019
ms.author: sutalasi
ms.openlocfilehash: 4943b730bb46ee00200d84faf95a7ccb069d3aa8
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59678955"
---
# <a name="replicate-azure-disk-encryption-enabled-virtual-machines-to-another-azure-region"></a>Az Azure Disk Encryption-kompatibilis virtuális gépek replikálása másik Azure-régióba

Ez a cikk ismerteti az Azure Disk Encryption-kompatibilis virtuális gépek replikálása egy Azure-régióból a másikba.

>[!NOTE]
>Az Azure Site Recovery jelenleg csak Azure virtuális gépeken futó, amelyek vagy egy Windows operációs rendszert futtató támogatja [engedélyezett a titkosítás az Azure Active Directoryval (Azure AD)](https://aka.ms/ade-aad-app).

## <a name="required-user-permissions"></a>Szükséges felhasználói engedélyek
A Site Recovery a felhasználónak szüksége van a key vault létrehozása az a cél régióban, és másolja a kulcsokat a régió.

A virtuális gépek lemeztitkosítás engedélyezve van az Azure Portalról a replikáció engedélyezése a felhasználónak a következő engedélyekkel:

- Key vault-engedélyek
    - Lista
    - Létrehozás
    - Lekérés

-   A Key vault titkos kód engedélyei
    - Lista
    - Létrehozás
    - Lekérés

- A Key vault Kulcsengedélyek (csak a virtuális gépek kulcstitkosítási kulcs segítségével titkosíthatja a lemeztitkosítási kulcsok esetén szükséges)
    - Lista
    - Lekérés
    - Létrehozás
    - Titkosítás
    - Visszafejtés

Kezeli az engedélyeket, nyissa meg a portálon a kulcstároló erőforrásához. Adja hozzá a szükséges engedélyekkel a felhasználó számára. Az alábbi példa bemutatja, hogyan engedélyeket a key vault *ContosoWeb2Keyvault*, azaz a forrásrégióban.

1. Lépjen a **kezdőlap** > **Keyvaults** > **ContosoWeb2KeyVault > hozzáférési házirendek**.

   ![A Key vault-engedélyek ablak](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-1.png)

2. Láthatja, hogy nincsenek-e nincs felhasználói engedélyeket. Válassza ki **új hozzáadása**. Adja meg a felhasználó és engedélyeit.

   ![keyvault-engedélyek](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-2.png)

Vészhelyreállítás (DR) program engedélyezése a felhasználó nem rendelkezik jogosultsággal a kulcsok másolása, ha egy megfelelő jogosultsággal rendelkező rendszergazda az alábbi parancsfájlt használhatja a titkosítási titkos kódok és kulcsok átmásolása a célrégióban.

Engedélyek a hibaelhárításhoz tekintse meg [key vault engedélyekkel kapcsolatos problémák](#trusted-root-certificates-error-code-151066) a cikk későbbi részében.

>[!NOTE]
>A virtuális gépek lemeztitkosítás engedélyezve van a portálról a replikáció engedélyezéséhez meg kell legalább "List" engedélyek a kulcstartók, titkos kulcsok és kulcsok.

## <a name="copy-disk-encryption-keys-to-the-dr-region-by-using-the-powershell-script"></a>A Vészhelyreállítási régióban lemez titkosítási kulcsok másolása a PowerShell-parancsfájl használatával

1. [Nyissa meg a "CopyKeys" nyers szkriptkódot](https://aka.ms/ade-asr-copy-keys-code).
2. Másolja a fájlba, és adja neki **másolási-keys.ps1**.
3. Nyissa meg a Windows PowerShell alkalmazást, és lépjen abba a mappába, ahová a fájlt mentette.
4. Másolás – keys.ps1 hajtható végre.
5. Adja meg, jelentkezzen be Azure hitelesítő adatait.
6. Válassza ki a **Azure-előfizetés** a virtuális gépeket.
7. Várjon, amíg az erőforráscsoportok betölteni, és válassza ki a **erőforráscsoport** a virtuális gépeket.
8. A megjelenő listában válassza ki a virtuális gépeket. Csak virtuális gépek, amelyek engedélyezve vannak a lemeztitkosítás vannak a listán.
9. Válassza ki a **célhelye**.

    - **Lemez titkosítása kulcstartók**
    - **Kulcstitkosítás kulcstartók**

   Alapértelmezés szerint a Site Recovery létrehoz egy új kulcstartót a célrégióban. A tároló neve tartalmaz egy "asr" utótaggal, a forrás virtuális gépek lemeztitkosítási kulcsok alapján. Ha a key vault már létezik a Site Recovery által létrehozott, újra. Válasszon másik kulcstartót a listából, ha szükséges.

## <a name="enable-replication"></a>A replikáció engedélyezése

Ebben a példában az elsődleges Azure-régióban, Kelet-Ázsia, és a másodlagos régióba Délkelet-Ázsia.

1. A tárolóban, válassza ki a **+ replikálás**.
2. Vegye figyelembe a következő mezőket.
    - **forrás**: A pont, a forrás virtuális gépet, amely ebben az esetben **Azure**.
    - **Forrás helye**: Az Azure-régió kívánja a virtuális gépek védelmét. Ebben a példában a forrás helye "Kelet-Ázsia."
    - **Üzemi modell**: Az Azure üzembehelyezési modell forrásgépek közül.
    - **Forrás-előfizetés**: Az előfizetés, a forrás virtuális gépeket, amelyekhez tartoznak. Minden olyan előfizetés, amely ugyanabban a Azure Active Directory-bérlőben, a recovery services-tároló lehet.
    - **Erőforráscsoport**: Az erőforráscsoport, a forrás virtuális gépeket, amelyekhez tartoznak. A kiválasztott erőforráscsoportba tartozó összes virtuális gépen a védelem a következő lépésben szerepelnek.

3. A **virtuális gépek** > **válassza ki a virtuális gépek**, jelölje be minden replikálni kívánt virtuális Géphez. Csak olyan gépeket választhat, amelyeken használható a replikáció funkció. Ezután válassza az **OK** lehetőséget.

4. A **beállítások**, a következő célhelyre beállításokat konfigurálhatja.

    - **Célhely**: A hely, ahol a forrás virtuális gép fognak replikálódni. A Site Recovery a kiválasztott gép helye alapján megfelelő régiók listáját tartalmazza. Azt javasoljuk, hogy ugyanazt a helyet használja a Recovery Services-tároló helye.
    - **Érintett előfizetés**: A célként megadott előfizetés, amely vész-helyreállítási szolgál. Alapértelmezés szerint a célként megadott előfizetés megegyezik a forrás-előfizetés.
    - **Céloldali erőforráscsoport**: Az erőforráscsoport, ahol az összes a replikált virtuális gépek tartoznak. Alapértelmezés szerint a Site Recovery létrehoz egy új erőforráscsoportot a célrégióban. A név lekéri az "asr" utótaggal. Ha az erőforráscsoport már létezik az Azure Site Recovery által létrehozott, a rendszer újra. Azt is beállíthatja testre szabni, a következő szakaszban látható módon. A céloldali erőforráscsoport helye lehet a régiót, a forrás virtuális gépeket üzemeltető kivételével bármelyik Azure-régióban.
    - **Cél virtuális hálózattal**: Alapértelmezés szerint a Site Recovery létrehoz egy új virtuális hálózat a célrégióban. A név lekéri az "asr" utótaggal. A forrás hálózati leképezve, és minden jövőbeli védelemhez használt. [További](site-recovery-network-mapping-azure-to-azure.md) információk a hálózatleképezésről.
    - **Cél tárfiókok (Ha a forrás virtuális gép nem használ felügyelt lemezeket)**: Alapértelmezés szerint a Site Recovery által a forrás virtuális gép tárkonfigurációt mimicking új céltárfiók hoz létre. Ha már létezik egy tárfiókot, a rendszer újra.
    - **Replikált felügyelt lemezek (Ha a forrásoldali virtuális gép felügyelt lemezeket használ)**: Site Recovery új replika felügyelt lemezeket hoz létre a célrégióban a forrás virtuális gép felügyelt lemezekre vonatkozó azonos tártípusban (standard vagy prémium), a forrás virtuális gép lemezét tükrözéséhez.
    - **Gyorsítótár tárfiókjai**: A Site Recovery egy extra nevű tárfiókot kell *tárolási gyorsítótár* a forrásrégióban. A forrásoldali virtuális gép az összes módosítást nyomon követni, és a gyorsítótárfiók küldött. Ezek még ezután a rendszer a célhelyen.
    - **A rendelkezésre állási csoport**: Alapértelmezés szerint a Site Recovery létrehoz egy új rendelkezésre állási csoportot a célrégióban. Neve az "asr" utótaggal rendelkezik. Ha a Site Recovery által már létrehozott rendelkezésre állási csoport létezik, az azt újra.
    - **Lemez titkosítása kulcstartók**: Alapértelmezés szerint a Site Recovery létrehoz egy új kulcstartót a célrégióban. Rendelkezik egy "asr" utótaggal, a forrás virtuális gépek lemeztitkosítási kulcsok alapján. Ha már létezik az Azure Site Recovery által már létrehozott egy kulcstartót, újra.
    - **A kulcstartók titkosító kulcs**: Alapértelmezés szerint a Site Recovery létrehoz egy új kulcstartót a célrégióban. A neve tartalmaz egy "asr" utótaggal, a forrás virtuális gép fő titkosítási kulcsok alapján. Ha már létezik az Azure Site Recovery által már létrehozott egy kulcstartót, újra.
    - **Replikációs házirend**: A helyreállítási pont megőrzési előzményekhez és az alkalmazáskonzisztens pillanatkép készítésének gyakorisága beállításait határozza meg. Alapértelmezés szerint a Site Recovery hoz létre egy új replikációs házirendet az alapértelmezett beállításokkal *24 óra* helyreállítási pontok megőrzésének ideje és *60 perc* az alkalmazáskonzisztens pillanatkép készítésének gyakorisága.

## <a name="customize-target-resources"></a>Célerőforrások testreszabása

Kövesse az alábbi lépéseket a Site Recovery alapértelmezett beállításainak módosítására.

1. Válassza ki **Testreszabás** melletti "Célként megadott előfizetés" az alapértelmezett célként megadott előfizetés módosításához. Válassza ki az előfizetést az Azure AD-bérlőhöz rendelkezésre álló előfizetések listájából.

2. Válassza ki **Testreszabás** melletti "erőforráscsoportot, hálózati, tárolási és rendelkezésre állási csoportok" módosítására az alábbi alapértelmezett beállításokkal:
    - A **céloldali erőforráscsoport**, válassza ki az erőforráscsoportot a listából a célként megadott helyen az előfizetés az erőforrás-csoportok.
    - A **cél virtuális hálózattal**, mely a célhelyen található virtuális hálózatok listájából válassza ki a hálózatot.
    - A **rendelkezésre állási csoport**, adhat hozzá rendelkezésre állási csoport beállításait a virtuális géphez, ha egy rendelkezésre állási csoportot a forrásrégióban részei.
    - A **cél tárfiókok**, válassza ki a használni kívánt fiók.

2. Válassza ki **Testreszabás** melletti "Titkosítási beállítások" módosítására az alábbi alapértelmezett beállításokkal:
   - A **cél lemez titkosítása a key vault**, válassza ki a cél lemez titkosítása a key vault a célként megadott helyen az előfizetés kulcstartók listájából.
   - A **cél kulcs titkosítása a key vault**, válassza ki a cél kulcs titkosítása a key vault a célként megadott helyen az előfizetés kulcstartók listájából.

3. Válassza ki **célként megadott erőforrás létrehozása** > **replikáció engedélyezése**.
4. Után a replikáció engedélyezve vannak a virtuális gépek, nézze meg a virtuális gépek állapota a **replikált elemek**.

>[!NOTE]
>Kezdeti replikálás során a az állapot frissítéséhez, nyilvánvaló folyamatban nélkül időbe telhet. Kattintson a **frissítése** beolvasni a legfrissebb állapotát.

## <a name="update-target-vm-encryption-settings"></a>Cél virtuális gép titkosítási beállításainak frissítése
A következő esetekben lesz szükség a cél virtuális gép titkosítási beállításainak frissítése:
  - Engedélyezte a virtuális gép Site Recovery replikációjára. Később engedélyezve van a forrásoldali virtuális gép lemeztitkosítás.
  - Engedélyezte a virtuális gép Site Recovery replikációjára. Módosította később, a lemez titkosítási kulcs vagy a kulcsalapú titkosítás kulcsa a forrásoldali virtuális Gépen.

Használhat [parancsfájl](#copy-disk-encryption-keys-to-the-dr-region-by-using-the-powershell-script) másolja a titkosítási kulcsokat a célrégióban, és frissítse az titkosítási beállításainak **Recovery services-tároló** > *replikált elem*  >  **Tulajdonságok** > **számítás és hálózat**.

![Frissítés ADE beállítások párbeszédablak](./media/azure-to-azure-how-to-enable-replication-ade-vms/update-ade-settings.png)

## <a id="trusted-root-certificates-error-code-151066"></a>A key vault engedélyekkel kapcsolatos problémák hibaelhárítása Azure – Azure VM-replikáció során

**1. ok:** Előfordulhat, hogy a kijelölt a cél régióból egy már létrehozott key vault, amely nem rendelkezik a szükséges engedélyekkel, ahelyett, hogy a Site Recovery hozzon létre egyet. Győződjön meg arról, hogy rendelkezik-e a key vault, a szükséges engedélyekkel, a fentebb leírt módon.

*Például*: A key vault rendelkező virtuális gép replikálása próbál *ContososourceKeyvault* a forrásrégióban található.
Minden engedély is van a forrás key vaultnak régióban. Azonban a védelem, során válasszon a már létrehozott key vault ContosotargetKeyvault, amely nem rendelkezik engedélyekkel. Hiba történik.

**Hogyan háríthatja el:** Lépjen a **kezdőlap** > **Keyvaults** > **ContososourceKeyvault** > **hozzáférési házirendek** és adja hozzá a megfelelő engedélyekkel.

**2. ok:** Előfordulhat, hogy kiválasztotta a cél régióból egy már létrehozott key vault, amely nem rendelkezik visszafejtése titkosítása engedélyek ahelyett, hogy a Site Recovery hozzon létre egyet. Győződjön meg arról, hogy rendelkezik-e visszafejtése-titkosítják az engedélyeket is amelyet épp titkosít a kulcs a forrásrégióban.</br>

*Például*: Replikálja a virtuális Gépet, amely rendelkezik key vault próbál *ContososourceKeyvault* a forrásrégióban található. A szükséges engedéllyel rendelkezik a forrás régió kulcstartón. Azonban a védelem, során válasszon a már létrehozott key vault ContosotargetKeyvault, amely nem rendelkezik engedélyekkel titkosítására és visszafejtésére. Hiba történik.</br>

**Hogyan háríthatja el:** Lépjen a **kezdőlap** > **Keyvaults** > **ContososourceKeyvault** > **hozzáférési házirendek**. Adjon hozzá engedélyeket a **Kulcsengedélyek** > **titkosítási műveletek**.

## <a name="next-steps"></a>További lépések

[További](site-recovery-test-failover-to-azure.md) feladatátvételi teszt futtatásáról.
