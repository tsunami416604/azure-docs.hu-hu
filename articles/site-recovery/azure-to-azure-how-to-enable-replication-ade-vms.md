---
title: Azure Disk Encryption-kompatibilis virtuális gépek replikálásának konfigurálása Azure Site Recoveryban | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan konfigurálható a Azure Disk Encryption-kompatibilis virtuális gépek replikálása egyik Azure-régióból a másikba Site Recovery használatával.
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/08/2019
ms.author: sutalasi
ms.openlocfilehash: 25f8c19ad1c574f6a3d84d50d911b4da6b52cfc3
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68516506"
---
# <a name="replicate-azure-disk-encryption-enabled-virtual-machines-to-another-azure-region"></a>Azure Disk Encryption-kompatibilis virtuális gépek replikálása egy másik Azure-régióba

Ez a cikk azt ismerteti, hogyan replikálhat Azure Disk Encryption-kompatibilis virtuális gépeket egyik Azure-régióból a másikba.

>[!NOTE]
>Azure Site Recovery jelenleg csak olyan Azure-beli virtuális gépeket támogat, amelyek Windows operációs rendszert futtatnak, és amelyek [Azure Active Directory (Azure ad) titkosításra vannak engedélyezve](https://aka.ms/ade-aad-app).

## <a id="required-user-permissions"></a>Szükséges felhasználói engedélyek
Site Recovery megköveteli a felhasználónak, hogy rendelkezzen a kulcstartó létrehozásához szükséges engedélyekkel, és a forrástartomány kulcstartóból másolja a kulcsokat a cél régió kulcstartóba.

Ha engedélyezni szeretné a lemezes titkosítást használó virtuális gépek replikálását a Azure Portalból, a felhasználónak a következő engedélyekkel kell rendelkeznie a **forrás-és a célként megadott régió** kulcstárolójában is.

- Key Vault-engedélyek
    - Listázás, létrehozás és beolvasás
    
- Key Vault – titkos engedélyek
    - Titkoskód-kezelési műveletek
        - Beolvasás, Listázás és beállítás
    
- Key Vault-kulcs engedélyei (csak akkor szükséges, ha a virtuális gépek titkosítási kulcs titkosítása a lemez titkosítási kulcsainak titkosítása érdekében)
    - Kulcskezelési műveletek
        - Beolvasás, Listázás és létrehozás
    - Titkosítási műveletek
        - Visszafejtés és titkosítás

Az engedélyek kezeléséhez nyissa meg a Key Vault-erőforrást a portálon. Adja meg a felhasználóhoz szükséges engedélyeket. Az alábbi példa bemutatja, hogyan engedélyezheti az engedélyeket a Key Vault *ContosoWeb2Keyvault*, amely a forrásoldali régióban található.

1. Nyissa meg a **Home** > **Vaults** > **ContosoWeb2KeyVault > hozzáférési házirendeket**.

   ![Key Vault-engedélyek ablak](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-1.png)

2. Láthatja, hogy nincsenek felhasználói engedélyek. Válassza az **új hozzáadása**elemet. Adja meg a felhasználó és az engedélyek adatait.

   ![Kulcstartó engedélyei](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-2.png)

Ha a vész-helyreállítást engedélyező felhasználó (DR) nem rendelkezik a kulcsok másolásához szükséges engedélyekkel, akkor a megfelelő engedélyekkel rendelkező biztonsági rendszergazda a következő parancsfájllal másolhatja a titkosítási titkokat és kulcsokat a célként megadott régióba.

Az engedélyek hibaelhárításához tekintse meg a cikk későbbi részében található [Key Vault engedélyekkel kapcsolatos problémákat](#trusted-root-certificates-error-code-151066) .

>[!NOTE]
>A lemezes titkosítást támogató virtuális gépek portálról történő replikálásának engedélyezéséhez legalább "List" engedélyekre van szükség a kulcstartók, titkok és kulcsok számára.

## <a name="copy-disk-encryption-keys-to-the-dr-region-by-using-the-powershell-script"></a>Lemezes titkosítási kulcsok másolása a DR régióba a PowerShell-parancsfájl használatával

1. [Nyissa meg a "CopyKeys" nyers parancsfájl kódját](https://aka.ms/ade-asr-copy-keys-code).
2. Másolja a szkriptet egy fájlba, és nevezze el **copy-Keys. ps1**néven.
3. Nyissa meg a Windows PowerShell alkalmazást, és keresse meg azt a mappát, ahová a fájlt mentette.
4. Futtassa a copy-Keys. ps1 programot.
5. Adja meg az Azure-beli hitelesítő adatokat a bejelentkezéshez.
6. Válassza ki a virtuális gépek **Azure** -előfizetését.
7. Várjon, amíg betölti az erőforráscsoportok betöltését, majd válassza ki a virtuális gépekhez tartozó **erőforráscsoportot** .
8. Válassza ki a virtuális gépeket a megjelenő listából. Csak a lemezes titkosításhoz engedélyezett virtuális gépek szerepelnek a listán.
9. Válassza kia célhelyet.

    - **Lemezes titkosítási kulcstartók**
    - **Key encryption Key vaultok**

   Alapértelmezés szerint a Site Recovery egy új kulcstartót hoz létre a célhelyen. A tár neve "ASR" utótaggal rendelkezik, amely a forrás virtuális gép lemezének titkosítási kulcsain alapul. Ha már létezik egy Site Recovery által létrehozott kulcstartó, azt a rendszer újra felhasználja. Ha szükséges, válasszon egy másik kulcstartót a listából.

## <a name="enable-replication"></a>Replikáció engedélyezése

Ebben a példában az elsődleges Azure-régió Kelet-Ázsia, a másodlagos régió pedig Dél-Kelet-Ázsia.

1. A tárolóban válassza a **+ replikálás**lehetőséget.
2. Jegyezze fel a következő mezőket.
    - **Forrás**: A virtuális gépek származási helye, amely ebben az esetben az **Azure**.
    - **Forrás helye**: Az az Azure-régió, ahol a virtuális gépeket el szeretné látni. Ebben a példában a forrás helye: "Kelet-Ázsia."
    - **Üzembe helyezési modell**: A forrásoldali gépek Azure-beli üzembe helyezési modellje.
    - **Forrás-előfizetés**: Az előfizetés, amelyhez a forrás virtuális gépek tartoznak. Minden olyan előfizetés lehet, amely ugyanabban a Azure Active Directory bérlőben található, mint a Recovery Services-tároló.
    - **Erőforráscsoport**: Az erőforráscsoport, amelyhez a forrás virtuális gépek tartoznak. A következő lépésben a kiválasztott erőforráscsoport összes virtuális gépe szerepel a védelemben.

3. **Virtual Machines** > **válassza a virtuális gépek lehetőséget**, és válassza ki a replikálni kívánt virtuális gépeket. Csak olyan gépeket választhat, amelyeken használható a replikáció funkció. Ezután válassza az **OK** lehetőséget.

4. A **Beállítások**területen a következő cél-hely beállításokat állíthatja be.

    - **Cél helye**: Az a hely, ahol a forrás virtuális gép adatait replikálja a rendszer. Site Recovery a kiválasztott gép helyétől függően a megfelelő célcsoportok listáját jeleníti meg. Azt javasoljuk, hogy ugyanazt a helyet használja, mint a Recovery Services tároló helye.
    - **Cél-előfizetés**: A katasztrófa utáni helyreállításhoz használt cél-előfizetés. Alapértelmezés szerint a cél-előfizetés megegyezik a forrás-előfizetéssel.
    - **Cél erőforráscsoport**: Az az erőforráscsoport, amelybe a replikált virtuális gépek tartoznak. Alapértelmezés szerint a Site Recovery új erőforráscsoportot hoz létre a célként megadott régióban. A név megkapja az "ASR" utótagot. Ha már létezik olyan erőforráscsoport, amelyet Azure Site Recovery hozott létre, a rendszer újra felhasználja. Azt is megteheti, hogy testreszabja, ahogy az a következő szakaszban is látható. A célként megadott erőforráscsoport helye bármely Azure-régió lehet, kivéve a forrásként szolgáló virtuális gépeket tároló régiót.
    - **Célként megadott virtuális hálózat**: Alapértelmezés szerint a Site Recovery új virtuális hálózatot hoz létre a céltartományban. A név megkapja az "ASR" utótagot. Ez a forrás-hálózatra van leképezve, és minden jövőbeli védelemhez használatos. [További](site-recovery-network-mapping-azure-to-azure.md) információ a hálózati leképezésről.
    - **Cél Storage-fiókok (ha a forrás virtuális gép nem használ felügyelt lemezeket)** : Alapértelmezés szerint a Site Recovery egy új célként megadott Storage-fiókot hoz létre a forrásként szolgáló virtuális gép tárolási konfigurációjának használatával. Ha már létezik egy Storage-fiók, az újra felhasználható.
    - **Replika által felügyelt lemezek (ha a forrás virtuális gép felügyelt lemezeket használ)** : Site Recovery új replika felügyelt lemezeket hoz létre a célhelyen a forrásként szolgáló virtuális gép felügyelt lemezei azonos tárolási típussal (standard vagy prémium) a forrás virtuális gép felügyelt lemezei alapján.
    - **Gyorsítótár-tárolási fiókok**: Site Recovery a forrás régióban a *cache Storage* nevű további Storage-fiókra van szükség. A rendszer a forrásként szolgáló virtuális gépek összes módosítását nyomon követi és továbbítja a gyorsítótárbeli Storage-fiókba. Ezután a rendszer replikálja őket a célhelyre.
    - **Rendelkezésre állási csoport**: Alapértelmezés szerint a Site Recovery új rendelkezésre állási készletet hoz létre a célként megadott régióban. A név "ASR" utótaggal rendelkezik. Ha Site Recovery által létrehozott rendelkezésre állási csoport már létezik, a rendszer újra felhasználja.
    - **Lemezes titkosítási kulcstartók**: Alapértelmezés szerint a Site Recovery egy új kulcstartót hoz létre a célhelyen. Egy "ASR" utótaggal rendelkezik, amely a forrás virtuális gép lemezének titkosítási kulcsain alapul. Ha Azure Site Recovery által létrehozott kulcstároló már létezik, az újra felhasználható.
    - **Key encryption Key vaultok**: Alapértelmezés szerint a Site Recovery egy új kulcstartót hoz létre a célhelyen. A név "ASR" utótaggal rendelkezik, amely a forrás virtuális gép kulcsának titkosítási kulcsain alapul. Ha Azure Site Recovery által létrehozott kulcstartó már létezik, akkor újra felhasználja.
    - **Replikációs házirend**: Meghatározza a helyreállítási pontok megőrzési előzményeinek és az alkalmazás-konzisztens Pillanatképek gyakoriságának beállításait. Alapértelmezés szerint a Site Recovery egy új replikációs házirendet hoz létre, amely a helyreállítási pontok megőrzésének *24 óráját* és *60 percet* vesz igénybe az alkalmazás-konzisztens pillanatkép gyakorisága esetében.

## <a name="customize-target-resources"></a>Cél erőforrások testreszabása

Az alábbi lépéseket követve módosíthatja a Site Recovery alapértelmezett célhelyének beállításait.

1. Az alapértelmezett cél-előfizetés módosításához kattintson a "cél előfizetés" elem melletti **Testreszabás** lehetőségre. Válassza ki az előfizetést az Azure AD-bérlőben elérhető előfizetések listájából.

2. A következő alapértelmezett beállítások módosításához válassza az "erőforráscsoport, hálózat, tárterület és rendelkezésre állási csoportok" melletti **Testreszabás** lehetőséget:
    - A **cél erőforráscsoport**területen válassza ki az erőforráscsoportot az előfizetés célhelyén található erőforráscsoportok listájából.
    - A **célként megadott virtuális hálózat**esetében válassza ki a hálózatot a célhelyen lévő virtuális hálózatok listájából.
    - A **rendelkezésre állási csoport**számára a rendelkezésre állási csoport beállításait hozzáadhatja a virtuális géphez, ha azok egy rendelkezésre állási csoport részét képezik a forrás régióban.
    - A **cél Storage-fiókok**esetében válassza ki a használni kívánt fiókot.

2. A következő alapértelmezett beállítások módosításához válassza a "titkosítási beállítások" melletti **Testreszabás** elemet:
   - A céllemez **titkosítási kulcstárolójának**kiválasztásához válassza ki a cél lemez titkosítási kulcstárolót az előfizetés célhelyén található kulcstartók listájából.
   - A **cél kulcsú titkosítási Key**Vault esetében válassza ki a célként megadott kulcs titkosítási kulcsának tárolóját az előfizetés célhelyén található kulcstartók listájából.

3. Válassza a **cél erőforrás** > létrehozása a**replikáció engedélyezése**lehetőséget.
4. Miután a virtuális gépek engedélyezettek a replikáláshoz, a virtuális gépek állapotát a **replikált elemek**területen tekintheti meg.

>[!NOTE]
>A kezdeti replikálás során előfordulhat, hogy az állapot frissítése hosszabb ideig is eltarthat. Kattintson a **frissítés** gombra a legutóbbi állapot lekéréséhez.

## <a name="update-target-vm-encryption-settings"></a>Cél virtuális gép titkosítási beállításainak frissítése
A következő esetekben frissítenie kell a cél virtuális gép titkosítási beállításait:
  - Engedélyezte Site Recovery replikációt a virtuális gépen. Később engedélyezte a lemez titkosítását a forrásoldali virtuális gépen.
  - Engedélyezte Site Recovery replikációt a virtuális gépen. Később megváltoztatta a lemez titkosítási kulcsát vagy a kulcs titkosítási kulcsát a forrás virtuális gépen.

A titkosítási kulcsokat a célhelyre másolhatja, majd frissítheti a cél titkosítási beállításokat a **helyreállítási** > tár*replikált elemek* > **tulajdonságaiban**  >  . [](#copy-disk-encryption-keys-to-the-dr-region-by-using-the-powershell-script) **Számítás és hálózat**.

![Az ADE-beállítások frissítése párbeszédpanel](./media/azure-to-azure-how-to-enable-replication-ade-vms/update-ade-settings.png)

## <a id="trusted-root-certificates-error-code-151066"></a>A Key Vault engedélyeivel kapcsolatos problémák elhárítása az Azure – Azure virtuális gépek replikálásakor

Azure Site Recovery legalább olvasási engedéllyel kell rendelkeznie a forrástartományban Key vaultban, és írási engedéllyel kell rendelkeznie a cél régió kulcstartóján a titkos kód beolvasásához és a célként megadott régió Key vaultba való másolásához. 

**1. ok:** A kulcsok olvasásához nincs "GET" engedély a **forrás régió kulcstartójában** . </br>
**A javítás módja:** Függetlenül attól, hogy Ön előfizetés-rendszergazda-e, vagy sem, fontos, hogy kérjen engedélyt a kulcstartón.

1. Lépjen a forrás régió Key vaultba, amely ebben a példában a "ContososourceKeyvault" > **hozzáférési szabályzatok** 
2. A **válasszon** felhasználói nevet a felhasználó kiválasztása területen például a következőt adja meg: "dradmin@contoso.com"
3. A **kulcs engedélyei** területen válassza a beolvasás lehetőséget. 
4. A **titok engedélye** területen válassza a beolvasás lehetőséget. 
5. A hozzáférési házirend mentése

**2. ok:** Nem rendelkezik a szükséges engedélyekkel a **cél régió Key vaultban** a kulcsok írásához. </br>

*Például*: Megpróbál replikálni egy olyan virtuális gépet, amely egy Key Vault- *ContososourceKeyvault* rendelkezik a forrásoldali régióban.
A forrástartomány Key Vault összes engedélyével rendelkezik. A védelem során azonban ki kell választania a már létrehozott Key Vault-ContosotargetKeyvault, amely nem rendelkezik engedélyekkel. Hiba történik.

A [célként megadott Key vaulthoz](#required-user-permissions) szükséges engedély

**A javítás módja:** Lépjena **Home** >  > kulcstartókContosotargetKeyvaulthozzáférési > szabályzatok**lehetőségre** , és adja hozzá a megfelelő engedélyeket.

## <a name="next-steps"></a>További lépések

[További](site-recovery-test-failover-to-azure.md) információ a feladatátvételi teszt futtatásáról.
