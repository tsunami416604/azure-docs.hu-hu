---
title: A titkosított Azure-virtuális gépek replikációjának engedélyezése az Azure Site Recovery szolgáltatásban
description: Ez a cikk bemutatja, hogyan konfigurálhatja a replikációt az Azure Lemeztitkosítást támogató virtuális gépek egyik Azure-régióból a másikba a Site Recovery használatával.
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 08/08/2019
ms.author: sutalasi
ms.openlocfilehash: 2bbb02df782439d934e96e7c16f28b9c11cc01fe
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408639"
---
# <a name="replicate-azure-disk-encryption-enabled-virtual-machines-to-another-azure-region"></a>Az Azure Disk Encryption-t engedélyező virtuális gépek replikálása egy másik Azure-régióba

Ez a cikk ismerteti, hogyan replikálható az Azure-beli virtuális gépek az Azure Disk Encryption (ADE) engedélyezve van, az egyik Azure-régióból a másikba.

>[!NOTE]
> A Site Recovery jelenleg támogatja az ADE szolgáltatást, az Azure Active Directory (AAD) használatával és anélkül windowsos operációs rendszereket futtató virtuális gépekhez. Linux operációs rendszerek esetén csak AAD nélküli ADE-t támogatunk. Ezenkívül az ADE 1.1-et futtató gépek (AAD nélkül) a virtuális gépeknek felügyelt lemezeket kell használniuk. A nem felügyelt lemezekkel rendelkező virtuális gépek nem támogatottak. Ha Az ADE 0.1-ről (AAD-vel) 1.1-re vált, az 1.1 engedélyezése után le kell tiltania a replikációt, és engedélyeznie kell a virtuális gép replikációját.


## <a name="required-user-permissions"></a><a id="required-user-permissions"></a>Szükséges felhasználói engedélyek
A Site Recovery megköveteli, hogy a felhasználó engedélyekkel hozza létre a kulcstartót a célrégióban, és másolja a kulcsokat a forrásrégió key vault a célrégió key vault.

A lemeztitkosítást támogató virtuális gépek azure-portálról történő replikációjának engedélyezéséhez a felhasználónak a következő engedélyekre van szüksége a **forrásrégióra és** a célrégió beli kulcstárolókra vonatkozóan.

- Kulcstartó engedélyei
    - Lista, létrehozás és bekerülés
    
- Titkos kulcstartó engedélyek
    - Titkos kezelési műveletek
        - Beszerez, Lista és Készlet
    
- A kulcstároló kulcsengedélyei (csak akkor szükségesek, ha a virtuális gépek kulcstitkosítási kulcsot használnak a lemeztitkosítási kulcsok titkosításához)
    - Kulcskezelési műveletek
        - Beszerez, Lista és Létrehozás
    - Kriptográfiai műveletek
        - Visszafejtés és titkosítás

Az engedélyek kezeléséhez nyissa meg a kapuban a key vault erőforrás. Adja hozzá a felhasználó számára szükséges engedélyeket. A következő példa bemutatja, hogyan engedélyezheti az engedélyeket a *ContosoWeb2Keyvault*, amely a forrásrégióban található.

1. Nyissa meg a**ContosoWeb2KeyVault > Access-házirendek** **otthoni** > **kulcstartóit.** > 

   ![A kulcstartó engedélyeinek ablaka](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-1.png)

2. Láthatja, hogy nincsenek felhasználói engedélyek. Válassza **az Új hozzáadása**lehetőséget. Adja meg a felhasználó és az engedélyek adatait.

   ![Keyvault-engedélyek](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-2.png)

Ha a vészhelyreállítást (DR) engedélyező felhasználó nem rendelkezik a kulcsok másolásához szükséges engedélyekkel, a megfelelő engedélyekkel rendelkező biztonsági rendszergazda a következő parancsfájl segítségével másolhatja a titkosítási titkos kulcsokat és kulcsokat a célrégióba.

Az engedélyek hibaelhárításához olvassa el a cikk [későbbi, key vault engedélyekkel kapcsolatos problémáit.](#trusted-root-certificates-error-code-151066)

>[!NOTE]
>A lemeztitkosítást támogató virtuális gépek replikálásához a portálról legalább "List" engedélyekre van szükség a kulcstartókhoz, titkos kulcsokhoz és kulcsokhoz.

## <a name="copy-disk-encryption-keys-to-the-dr-region-by-using-the-powershell-script"></a>Lemeztitkosítási kulcsok másolása a VÉSZ-régióba a PowerShell-parancsfájl használatával

1. [Nyissa meg a "CopyKeys" nyers parancsfájlkódot](https://aka.ms/ade-asr-copy-keys-code).
2. Másolja a parancsfájlt egy fájlba, és nevezze **el Copy-keys.ps1 fájlba.**
3. Nyissa meg a Windows PowerShell alkalmazást, és lépjen arra a mappára, ahová a fájlt mentette.
4. Copy-keys.ps1 fájl végrehajtása.
5. Adja meg az Azure-hitelesítő adatokat a bejelentkezéshez.
6. Válassza ki a virtuális gépek **Azure-előfizetését.**
7. Várja meg, amíg az erőforráscsoportok betöltődnek, majd válassza ki a virtuális gépek **erőforráscsoportját.**
8. Válassza ki a virtuális gépeket a megjelenő listából. Csak a lemeztitkosításra engedélyezett virtuális gépek szerepelnek a listán.
9. Válassza ki a **célhelyet**.

    - **Lemeztitkosítási kulcstartók**
    - **Kulcstitkosítási kulcstartók**

   Alapértelmezés szerint a Site Recovery új kulcstartót hoz létre a célrégióban. A tároló neve egy "asr" utótag, amely a forrás virtuális gép lemez titkosítási kulcsok alapján. Ha már létezik olyan kulcstartó, amelyet a Site Recovery hozott létre, a program újra felhasználja azt. Szükség esetén válasszon másik kulcstartót a listából.

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
    - **Célerőforrás-csoport:** Az az erőforráscsoport, amelyhez az összes replikált virtuális gép tartozik. Alapértelmezés szerint a Site Recovery új erőforráscsoportot hoz létre a célrégióban. A név az "asr" utótagot kapja. Ha már létezik olyan erőforráscsoport, amelyet az Azure Site Recovery hozott létre, újra fel használja. Testre is szabhatja, ahogy az a következő szakaszban is látható. A célerőforrás-csoport helye bármely Azure-régió lehet, kivéve azt a régiót, ahol a forrás virtuális gépek vannak tárolva.
    - **Virtuális hálózat célzása**: Alapértelmezés szerint a Site Recovery új virtuális hálózatot hoz létre a célrégióban. A név az "asr" utótagot kapja. A forráshálózathoz van rendelve, és bármilyen jövőbeli védelemre használható. [További információ](site-recovery-network-mapping-azure-to-azure.md) a hálózati hozzárendelésről.
    - **Céltárfiókok (ha a forrás virtuális gép nem használ felügyelt lemezeket) :** Alapértelmezés szerint a Site Recovery új céltárfiókot hoz létre a forrás virtuálisgép-tároló konfigurációjának utánzásával. Ha egy tárfiók már létezik, a rendszer újra felhasználja.
    - **Replika felügyelt lemezek (ha a forrás virtuális gép felügyelt lemezeket használ)**: Site Recovery új replika felügyelt lemezek a célrégióban, hogy tükrözze a forrás virtuális gép felügyelt lemezek azonos tárolótípusú (standard vagy prémium) a forrás virtuális gép felügyelt lemezek.
    - **Gyorsítótár-tároló fiókok:** Site Recovery szüksége van egy extra tárfiók nevű *gyorsítótár-tároló* a forrásrégióban. A forrás virtuális gépek összes változását nyomon követi nyomon, és elküldi a gyorsítótár-tárfiókba. Ezután replikálódiknak a célhelyre.
    - **Rendelkezésre állási készlet:** Alapértelmezés szerint a Site Recovery új rendelkezésre állási készletet hoz létre a célrégióban. A név "asr" utótaggal rendelkezik. Ha a Site Recovery által létrehozott rendelkezésre állási csoport már létezik, a program újra felhasználja azt.
    - **Lemeztitkosítási kulcstartók:** Alapértelmezés szerint a Site Recovery új kulcstartót hoz létre a célrégióban. Ez birtokol egy "asr" utótag, amely a forrás virtuális gép lemez titkosítási kulcsok alapján. Ha az Azure Site Recovery által létrehozott key vault már létezik, újra fel van használva.
    - **Kulcstitkosítási kulcstartók:** Alapértelmezés szerint a Site Recovery új kulcstartót hoz létre a célrégióban. A név egy "asr" utótag, amely a forrás virtuálisgép kulcstitkosítási kulcsok alapján. Ha az Azure Site Recovery által létrehozott key vault már létezik, újra felkell használni.
    - **Replikációs házirend**: A helyreállítási pontok megőrzési előzményeiés az alkalmazáskonzisztens pillanatkép gyakoriságának beállításait határozza meg. Alapértelmezés szerint a Site Recovery új replikációs házirendet hoz létre, amelynek alapértelmezett beállításai *24 óra* a helyreállítási pontok megőrzésére, és *60 perc* az alkalmazáskonzisztens pillanatképek gyakoriságára.

## <a name="customize-target-resources"></a>Célerőforrások testreszabása

A Site Recovery alapértelmezett célbeállításainak módosításához kövesse az alábbi lépéseket.

1. Válassza a **Testreszabás** lehetőséget a "Cél-előfizetés" elem mellett az alapértelmezett cél-előfizetés módosításához. Válassza ki az előfizetést az Azure AD-bérlőben elérhető előfizetések listájából.

2. Válassza az "Erőforráscsoport, hálózat, tárhely és rendelkezésre állás" elem melletti **Testreszabás** lehetőséget a következő alapértelmezett beállítások módosításához:
    - A **Cél erőforráscsoport**csoport ban válassza ki az erőforráscsoportot az erőforráscsoportok listájából az előfizetés célhelyén.
    - A **Cél virtuális hálózat**, válassza ki a hálózatot a listában a virtuális hálózatok a célhelyen.
    - Az **elérhetőségi készlet**hez hozzáadhatja a rendelkezésre állási beállításokat a virtuális géphez, ha azok a forrásrégióban lévő rendelkezésre állási csoport részét képezik.
    - A **Céltár-fiókok esetében**válassza ki a használni kívánt fiókot.

2. A "Titkosítási beállítások" elem melletti **Testreszabás** lehetőséget a következő alapértelmezett beállítások módosításához válassza:
   - A **céllemez-titkosítási kulcstartó,** válassza ki a céllemez titkosítási kulcstartó a kulcstartók listájából az előfizetés célhelyén.
   - A **célkulcs-titkosítási kulcstartó,** válassza ki a célkulcs titkosítási kulcstartó a kulcstartók listájából az előfizetés célhelyén.

3. Válassza **a Célerőforrás** > **létrehozása replikáció engedélyezése**lehetőséget.
4. Miután a virtuális gépek engedélyezve vannak a replikációhoz, ellenőrizheti a virtuális gépek állapotát a **Replikált elemek csoportban.**

>[!NOTE]
>A kezdeti replikáció során az állapot frissítése némi időt vehet igénybe, látszólagos előrehaladás nélkül. A legújabb állapot bekerüléséhez kattintson a **Frissítés** gombra.

## <a name="update-target-vm-encryption-settings"></a>Célvirtuális gép titkosítási beállításainak frissítése
A következő esetekben frissítenie kell a cél virtuális gép titkosítási beállításait:
  - Engedélyezte a site recovery replikációt a virtuális gépen. Később engedélyezte a lemeztitkosítást a forrásvirtuális gépen.
  - Engedélyezte a site recovery replikációt a virtuális gépen. Később módosította a lemeztitkosítási kulcsot vagy a kulcstitkosítási kulcsot a forrásvirtuális gépen.

Parancsfájl [segítségével](#copy-disk-encryption-keys-to-the-dr-region-by-using-the-powershell-script) átmásolhatja a titkosítási kulcsokat a célterületre, majd frissítheti a céltitkosítási beállításokat a **Helyreállítási szolgáltatások tárolóreplikált** > *elem* > **tulajdonságai nak tulajdonságai és** > **a hálózat**mezőben.

![Az ADE-beállítások frissítése párbeszédpanel ablaka](./media/azure-to-azure-how-to-enable-replication-ade-vms/update-ade-settings.png)

## <a name="troubleshoot-key-vault-permission-issues-during--azure-to-azure-vm-replication"></a><a id="trusted-root-certificates-error-code-151066"></a>Kulcstartó engedélyekkel kapcsolatos problémák elhárítása az Azure-ból Azure virtuális gép replikációja során

Az Azure Site Recovery legalább olvasási engedélyt igényel a forrás régió key vault és írási engedélyt a célrégió key vault olvasni a titkos kulcsot, és másolja a célrégió key vault. 

**1. ok:** Nincs "GET" engedéllyel a **forrásrégióban Key vault** a kulcsok olvasásához. </br>
**Hogyan lehet kijavítani:** Függetlenül attól, hogy ön egy előfizetés-rendszergazda, vagy sem, fontos, hogy engedélyt kapjon a key vault.

1. Lépjen a forrásrégióba: Kulcstartó, amely ebben a példában a "ContososourceKeyvault" > **Access-házirendek** 
2. Az **Egyszerű kijelölés csoportban** adjadradmin@contoso.comhozzá a felhasználónevét például: "
3. A **Kulcsengedélyek csoportban** válassza a GET (GET) lehetőséget. 
4. A **Titkos engedély csoportban** válassza a GET (GET) lehetőséget. 
5. A hozzáférési házirend mentése

**2. ok:** A kulcsok írásához nincs szükséges engedélye a **célrégió kulcstartójában.** </br>

*Például:* Megpróbál replikálni egy virtuális gép, amely rendelkezik *a contososourceKeyvault* key vault egy forrásrégióban.
A forrásrégió kulcstartójára vonatkozó összes engedéllyel rendelkezik. De a védelem során kiválasztja a már létrehozott key vault ContosotargetKeyvault, amely nem rendelkezik engedélyekkel. Hiba történik.

A [célkulcstartóhoz](#required-user-permissions) szükséges engedély

**Hogyan lehet kijavítani:** Nyissa meg a**ContosotargetKeyvault** > **Access-házirendeket,** **Home** > **Keyvaults** > és adja meg a megfelelő engedélyeket.

## <a name="next-steps"></a>További lépések

[További információ](site-recovery-test-failover-to-azure.md) a tesztfeladat-átvétel futtatásáról.
