---
title: A virtuális gépek igény szerinti elérése a Azure Security Centerban | Microsoft Docs
description: Ez a dokumentum azt mutatja be, hogy az Azure Security Center az Azure-beli virtuális gépekhez való hozzáférés szabályozása miként segít az igény szerinti virtuálisgép-hozzáférésben.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: fb092a3c6b473680480c3bba0ad6f437176833de
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73576404"
---
# <a name="manage-virtual-machine-access-using-just-in-time"></a>A virtuális gépekhez való hozzáférés kezelése igény szerint

Az igény szerinti (JIT) virtuálisgép-hozzáférés lehetővé teszi az Azure-beli virtuális gépek bejövő forgalmának zárolását, így csökkentve a támadásoknak való kitettséget, miközben könnyű hozzáférést biztosít a virtuális gépekhez, ha szükséges.

> [!NOTE]
> Az igény szerinti funkció a Security Center standard szintű csomagjában érhető el. A Security Center tarifacsomagjaival kapcsolatos további információért lásd a [díjszabást](security-center-pricing.md).


> [!NOTE]
> Security Center az igény szerinti virtuálisgép-hozzáférés jelenleg csak a Azure Resource Manager használatával telepített virtuális gépeket támogatja. Ha többet szeretne megtudni a klasszikus és a Resource Manager-alapú üzemi modellekről, tekintse meg a Azure Resource Manager és a [klasszikus telepítést](../azure-resource-manager/resource-manager-deployment-model.md)ismertető témakört.

## <a name="attack-scenario"></a>Támadási forgatókönyv

A találgatásos támadások általában a felügyeleti portok megcélzására szolgálnak, hogy hozzáférjenek a virtuális gépekhez. Ha ez sikeres, a támadó átveheti a vezérlést a virtuális gépen, és megadhat egy lábát a környezetében.

A találgatásos támadásoknak való kitettség csökkentése az egyik lehetőség, hogy korlátozza a portok megnyitásának időtartamát. A felügyeleti portokat nem kell mindig megnyitnia. Csak akkor kell megnyitnia őket, amikor csatlakozik a virtuális géphez, például felügyeleti vagy karbantartási feladatok elvégzéséhez. Ha az igény szerinti hozzáférés engedélyezve van, a Security Center a [hálózati biztonsági csoport](../virtual-network/security-overview.md#security-rules) (NSG) és a Azure Firewall szabályok használatával korlátozza a felügyeleti portok elérését, hogy a támadók ne tudják megcélozni azokat.

![Igény szerinti forgatókönyv](./media/security-center-just-in-time/just-in-time-scenario.png)

## <a name="how-does-jit-access-work"></a>Hogyan működik az JIT-hozzáférés?

Ha engedélyezve van az igény szerinti idő, Security Center az Azure-beli virtuális gépek felé irányuló bejövő forgalmat egy NSG-szabály létrehozásával zárolja. Válassza ki a virtuális gépen azokat a portokat, amelyeken a bejövő forgalom le lesz zárva. Ezeket a portokat az igény szerinti megoldás vezérli.

Amikor egy felhasználó hozzáférést kér egy virtuális géphez, Security Center ellenőrzi, hogy a felhasználó rendelkezik [-e szerepköralapú Access Control (RBAC)](../role-based-access-control/role-assignments-portal.md) engedélyekkel az adott virtuális géphez. Ha a kérést jóváhagyják, Security Center automatikusan konfigurálja a hálózati biztonsági csoportokat (NSG), és Azure Firewall, hogy engedélyezze a bejövő forgalmat a kiválasztott portokra és a kért forrás IP-címekre vagy tartományokra vonatkozóan a megadott időtartamig. Az idő lejárta után Security Center visszaállítja a NSG az előző állapotokra. A már létrehozott kapcsolatok azonban nem szakadnak meg.

 > [!NOTE]
 > Ha egy Azure Firewall mögötti virtuális géphez jóváhagy egy JIT hozzáférési kérelmet, akkor Security Center automatikusan módosítja a NSG és a tűzfal házirend-szabályait is. A megadott időtartamra vonatkozóan a szabályok engedélyezik a bejövő forgalmat a kiválasztott portokra és a kért forrás IP-címekre vagy tartományokra. Az idő elteltével Security Center visszaállítja a tűzfal és a NSG szabályait az előző állapotba.


## <a name="permissions-needed-to-configure-and-use-jit"></a>A JIT konfigurálásához és használatához szükséges engedélyek

| A felhasználók engedélyezése: | Beállított engedélyek|
| --- | --- |
| Egy virtuális gép JIT-szabályzatának konfigurálása vagy szerkesztése | *Rendelje hozzá ezeket a műveleteket a szerepkörhöz:*  <ul><li>A virtuális géphez társított előfizetés vagy erőforráscsoport hatókörén:<br/> ```Microsoft.Security/locations/jitNetworkAccessPolicies/write``` </li><li> Az előfizetés vagy az erőforráscsoport vagy a virtuális gép hatókörén: <br/>```Microsoft.Compute/virtualMachines/write```</li></ul> | 
| ||
|JIT-hozzáférés kérése egy virtuális géphez | *Rendelje hozzá ezeket a műveleteket a felhasználóhoz:*  <ul><li>A virtuális géphez társított előfizetés vagy erőforráscsoport hatókörén:<br/>  ```Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action``` </li><li>  Az előfizetés vagy az erőforráscsoport vagy a virtuális gép hatókörén:<br/> ```Microsoft.Compute/virtualMachines/read``` </li></ul>|


## <a name="configure-jit-on-a-vm"></a>JIT konfigurálása virtuális gépen

A JIT-szabályzatok konfigurálásának három módja van a virtuális gépen:

- [JIT-hozzáférés konfigurálása Azure Security Center](#jit-asc)
- [JIT-hozzáférés konfigurálása Azure-beli virtuális gépek paneljén](#jit-vm)
- [JIT-szabályzat konfigurálása virtuális gépen programozott módon](#jit-program)

## <a name="configure-jit-in-security-center"></a>A JIT konfigurálása Security Center

A Security Centerban beállíthat egy JIT-szabályzatot, és egy JIT-házirenddel egy virtuális géphez való hozzáférést igényelhet.


### JIT-hozzáférés konfigurálása Security Center-beli virtuális gépen<a name="jit-asc"></a>

1. Nyissa meg a **Security Center** irányítópultját.

2. A bal oldali panelen válassza az igény szerinti virtuális gépekhez **való hozzáférést**.

    ![Igény szerinti virtuális gépekhez való hozzáférés csempe](./media/security-center-just-in-time/just-in-time.png)

    Megnyílik az igény szerinti **VM-hozzáférés** ablak.

      ![Igény szerinti hozzáférés engedélyezése](./media/security-center-just-in-time/enable-just-in-time.png)

    Az igény szerinti virtuálisgép- **hozzáférés** információt nyújt a virtuális gépek állapotáról:

    - **Konfigurálva** – a virtuális gépek igény szerinti elérésének támogatásához konfigurált virtuális gépek. A bemutatott adatok az elmúlt héten szerepelnek, és minden virtuális géphez a jóváhagyott kérések száma, az utolsó hozzáférés dátuma és időpontja, valamint az utolsó felhasználó szerepel.
    - **Ajánlott** – a virtuális gépek igény szerinti elérését támogató virtuális gépek, de nem lettek konfigurálva a szolgáltatáshoz. Javasoljuk, hogy engedélyezze az igény szerinti virtuálisgép-hozzáférés-vezérlést ezekhez a virtuális gépekhez.
    - **No recommendation** (Nincs javaslat) – A virtuális gépek a következő okokból kerülhetnek ebbe a kategóriába:
      - Hiányzó NSG – az igény szerinti megoldáshoz szükség van egy NSG.
      - Klasszikus virtuális gép – Security Center igény szerinti virtuálisgép-hozzáférés jelenleg csak a Azure Resource Manager használatával telepített virtuális gépeket támogatja. Az igény szerinti megoldás nem támogatja a klasszikus üzembe helyezést. 
      - Egyéb – a virtuális gép ebben a kategóriában van, ha az igény szerinti megoldás ki van kapcsolva az előfizetés vagy az erőforráscsoport biztonsági házirendjében, vagy ha a virtuális gép hiányzik egy nyilvános IP-cím, és nem rendelkezik NSG.

3. Válassza az **ajánlott** lapot.

4. A **virtuális gép**területen kattintson az engedélyezni kívánt virtuális gépekre. Ekkor a virtuális gép mellett egy pipa is megjelenik.

5. Kattintson **a JIT engedélyezése virtuális gépeken**elemre.
   -. Ez a panel a Azure Security Center által javasolt alapértelmezett portokat jeleníti meg:
      - 22 – SSH
      - 3389 – RDP
      - 5985 – WinRM 
      - 5986 – WinRM
6. Emellett egyéni portokat is konfigurálhat:

      1. Kattintson az **Add** (Hozzáadás) parancsra. Megnyílik a **port hozzáadása konfiguráció** ablak.
      2. Minden olyan port esetében, amelyet úgy szeretne konfigurálni, hogy az alapértelmezett és az egyéni beállítást is testreszabja, a következő beállításokat szabhatja testre:

    - **Protokoll típusa**– a kérelem jóváhagyásakor a porton engedélyezett protokoll.
    - **Engedélyezett forrás IP-címek**– a kérés jóváhagyásakor a porton engedélyezett IP-címtartományok.
    - **Kérelmek maximális ideje**– az a maximális időtartam, ameddig egy adott port megnyitható.

     3. Kattintson az **OK** gombra.

1. Kattintson a **Save** (Mentés) gombra.

> [!NOTE]
>Ha a JIT virtuális gép hozzáférése engedélyezve van egy virtuális gépen, Azure Security Center létrehozza az összes bejövő forgalom megtagadása szabályt a kiválasztott portokhoz a társított és Azure Firewall hálózati biztonsági csoportokban. Ha a kiválasztott portokhoz más szabályok lettek létrehozva, akkor a meglévő szabályok prioritást élveznek az új "minden bejövő forgalom tiltása" szabályban. Ha nincsenek meglévő szabályok a kiválasztott portokon, akkor az új "minden bejövő forgalom tiltása" szabály kiemelt prioritást élvez a hálózati biztonsági csoportokban és Azure Firewall.


## <a name="request-jit-access-via-security-center"></a>JIT-hozzáférés kérése Security Centeron keresztül

Virtuális géphez való hozzáférés kérése Security Center használatával:

1. A **virtuális gépek igény szerinti elérése**területen válassza a **konfigurált** lapot.

2. A **virtuális gép**területen kattintson arra a virtuális gépre, amelyhez hozzáférést szeretne kérni. Ekkor megjelenik a virtuális gép melletti pipa.


    - A **kapcsolat részletei** oszlopban látható ikon jelzi, hogy a JIT engedélyezve van-e a NSG vagy az FW-on. Ha mindkettőn engedélyezve van, csak a tűzfal ikon jelenik meg.

    - A **kapcsolat részletei** oszlop a virtuális gép csatlakoztatásához és a nyitott portok eléréséhez szükséges információkat tartalmazza.

      ![Igény szerinti hozzáférés igénylése](./media/security-center-just-in-time/request-just-in-time-access.png)

3. Kattintson a **hozzáférés kérése**elemre. Megnyílik a **kérelem hozzáférése** ablak.

      ![JIT-részletek](./media/security-center-just-in-time/just-in-time-details.png)

4. A **kérelmek hozzáférése**területen minden egyes virtuális gép esetében konfigurálja a megnyitni kívánt portokat, valamint a port megnyitásának forrás IP-címeit, valamint azt az időablakot, amelyre a port megnyílik. Csak az igény szerinti szabályzatban konfigurált portok elérésére lesz lehetséges hozzáférés. Minden porthoz az igény szerinti házirendből származtatott maximális időtartam tartozik.

5. Kattintson a **portok megnyitása**lehetőségre.

> [!NOTE]
> Ha egy hozzáférést kérő felhasználó proxy mögött van, akkor az **IP-cím** nem működik. Előfordulhat, hogy meg kell határoznia a szervezet teljes IP-címtartományt.

## <a name="edit-a-jit-access-policy-via-security-center"></a>JIT hozzáférési szabályzat szerkesztése Security Center használatával

A virtuális gép meglévő igény szerinti házirendjét a virtuális gép védelméhez szükséges új port hozzáadásával és konfigurálásával, vagy egy már védett portra vonatkozó egyéb beállítások módosításával módosíthatja.

Egy virtuális gép meglévő, igény szerinti házirendjének szerkesztése:
1. A **konfigurált** lapon a virtuális **gépek**területen válassza ki azt a virtuális gépet, amelyhez a portot hozzá szeretné adni, kattintson az adott virtuális gép sorában lévő három pontra. 

1. Válassza a **Szerkesztés** elemet.
1. A **JIT VM-hozzáférés konfigurálása**területen szerkesztheti a már védett portok meglévő beállításait, vagy hozzáadhat egy új egyéni portot is. 
  ![JIT VM-hozzáférés](./media/security-center-just-in-time/edit-policy.png)

## <a name="audit-jit-access-activity-in-security-center"></a>A JIT hozzáférési tevékenység naplózása Security Center

A naplóbeli kereséssel betekintést nyerhet a virtuális gépek tevékenységeibe. Naplók megtekintése:

1. A **virtuális gépek igény szerinti elérése**területen válassza a **konfigurált** lapot.
2. A virtuális **gépek**területen válasszon ki egy virtuális gépet az adott virtuális gép sorában lévő három pontra kattintva, majd a menüben válassza a **tevékenység napló** elemet. Megnyílik a **tevékenység naplója** .

   ![Műveletnapló kiválasztása](./media/security-center-just-in-time/select-activity-log.png)

   A **műveletnapló** az adott virtuális géphez tartozó korábbi műveletek szűrt nézetét, valamint időt, dátumot és előfizetést biztosít.

A napló adatainak letöltéséhez **kattintson ide az összes elem CSV-ként való letöltéséhez**.

Módosítsa a szűrőket, és kattintson az **alkalmaz** gombra a keresés és a napló létrehozásához.



## JIT-hozzáférés konfigurálása Azure-beli virtuális gép oldaláról<a name="jit-vm"></a>

Az Ön kényelme érdekében közvetlenül a JIT használatával tud csatlakozni a virtuális GÉPHEZ a Security Center-beli virtuális gép oldaláról.

### <a name="configure-jit-access-on-a-vm-via-the-azure-vm-page"></a>JIT-hozzáférés konfigurálása virtuális gépen az Azure-beli virtuális gép oldalán keresztül

Ahhoz, hogy könnyen el lehessen érni a virtuális gépek igény szerinti elérését, beállíthatja, hogy a virtuális gép csak az igény szerinti hozzáférést engedélyezze közvetlenül a virtuális gépről.

1. A Azure Portal válassza a **virtuális gépek**lehetőséget.
2. Kattintson arra a virtuális gépre, amelyre korlátozni szeretné az igény szerinti hozzáférést.
3. A menüben kattintson a **konfiguráció**elemre.
4. Az igény szerinti **hozzáférés** területen kattintson az igény szerinti **házirend engedélyezése**lehetőségre. 

Ez a virtuális gép igény szerinti elérését teszi lehetővé a következő beállításokkal:

- Windows-kiszolgálók:
    - RDP-port 3389
    - Legfeljebb három órányi hozzáférés engedélyezett
    - Az engedélyezett forrás IP-címek beállítása bármelyik
- Linux-kiszolgálók:
    - SSH-port – 22
    - Legfeljebb három órányi hozzáférés engedélyezett
    - Az engedélyezett forrás IP-címek beállítása bármelyik
     
Ha egy virtuális gépen már van engedélyezve az igény szerinti kapcsolat, a konfigurációs lapjára kattintva láthatja, hogy az igény szerinti időpont engedélyezve van, és a hivatkozás használatával megnyithatja a szabályzatot Azure Security Center a beállítások megtekintéséhez és módosításához.

![JIT-konfiguráció a virtuális gépen](./media/security-center-just-in-time/jit-vm-config.png)

### <a name="request-jit-access-to-a-vm-via-the-azure-vm-blade"></a>JIT-hozzáférés kérése egy virtuális géphez az Azure virtuális gép paneljén keresztül

Ha a Azure Portal egy virtuális géphez próbál csatlakozni, az Azure ellenőrzi, hogy van-e az adott virtuális gépen beállított igény szerinti hozzáférési szabályzat.

- Ha a virtuális gépen van egy JIT-szabályzat konfigurálva, a **hozzáférés kérése** lehetőségre kattintva engedélyezheti, hogy a virtuális gép JIT-házirendjének megfelelően legyen hozzáférése. 

  >![JIT-kérelem](./media/security-center-just-in-time/jit-request.png)

  A hozzáférés kérése a következő alapértelmezett paraméterekkel történik:

  - **forrás IP-címe**: "any" (*) (nem módosítható)
  - **időtartomány**: három óra (nem módosítható) <!--Isn't this set in the policy-->
  - **portszám** RDP-port 3389 for Windows/a 22-es port a Linux rendszerhez (módosítható)

    > [!NOTE]
    > A Azure Firewall által védett virtuális gépekre vonatkozó kérések jóváhagyása után Security Center biztosítja a felhasználó számára a megfelelő kapcsolódási adatokat (a port hozzárendelését a DNAT táblából) a virtuális géphez való csatlakozáshoz.

- Ha nincs beállítva JIT a virtuális gépen, a rendszer kérni fogja, hogy állítson be egy JIT-szabályzatot.

  ![JIT-kérés](./media/security-center-just-in-time/jit-prompt.png)

## JIT-szabályzat konfigurálása virtuális gépen programozott módon<a name="jit-program"></a>

A REST API-kon és a PowerShellen keresztül igény szerint állíthatja be és használhatja.

## <a name="jit-vm-access-via-rest-apis"></a>JIT VM-hozzáférés REST API-kon keresztül

Az igény szerinti virtuálisgép-hozzáférési szolgáltatás a Azure Security Center API-n keresztül is használható. A konfigurált virtuális gépekről információt kaphat, újakat adhat hozzá, hozzáférést kérhet egy virtuális géphez, és további információkat érhet el ezen az API-n keresztül. Az igény szerinti REST API megismeréséhez tekintse meg a [JIT hálózati hozzáférési szabályzatokat](https://docs.microsoft.com/rest/api/securitycenter/jitnetworkaccesspolicies).

## <a name="jit-vm-access-via-powershell"></a>JIT VM-hozzáférés a PowerShell használatával

Az igény szerinti virtuálisgép-hozzáférési megoldás PowerShell használatával történő használatához használja a hivatalos Azure Security Center PowerShell-parancsmagokat, és kifejezetten `Set-AzJitNetworkAccessPolicy`.

Az alábbi példa egy igény szerinti virtuálisgép-hozzáférési szabályzatot állít be egy adott virtuális gépen, és beállítja a következőket:

1.  Zárjuk be a 22-es és a 3389-as portot.

2.  Állítsa be a maximális időablakot 3 órán belül, hogy minden jóváhagyott kérelemben megnyitható legyen.
3.  Lehetővé teszi a hozzáférést kérő felhasználó számára a forrás IP-címek vezérlését, és lehetővé teszi, hogy a felhasználó sikeres munkamenetet hozzon létre egy jóváhagyott, igény szerinti hozzáférési kérelem alapján.

Futtassa az alábbi parancsot a PowerShellben a következő végrehajtásához:

1.  Rendeljen hozzá egy olyan változót, amely a virtuális gép igény szerinti virtuálisgép-hozzáférési szabályzatát tárolja:

        $JitPolicy = (@{
         id="/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME"
        ports=(@{
             number=22;
             protocol="*";
             allowedSourceAddressPrefix=@("*");
             maxRequestAccessDuration="PT3H"},
             @{
             number=3389;
             protocol="*";
             allowedSourceAddressPrefix=@("*");
             maxRequestAccessDuration="PT3H"})})

2.  Helyezze be a virtuális gép igény szerinti hozzáférési szabályzatát egy tömbbe:
    
        $JitPolicyArr=@($JitPolicy)

3.  A virtuális gép igény szerinti hozzáférési szabályzatának konfigurálása a kiválasztott virtuális gépen:
    
        Set-AzJitNetworkAccessPolicy -Kind "Basic" -Location "LOCATION" -Name "default" -ResourceGroupName "RESOURCEGROUP" -VirtualMachine $JitPolicyArr 

#### <a name="request-access-to-a-vm-via-powershell"></a>Hozzáférés kérése virtuális géphez a PowerShell használatával

A következő példában egy igény szerinti virtuálisgép-hozzáférési kérést láthat egy adott virtuális géphez, amelyben a 22-es portot egy adott IP-cím és egy adott időtartam esetében kérik le:

Futtassa a következőt a PowerShellben:
1.  A virtuálisgép-kérelem hozzáférési tulajdonságainak konfigurálása

        $JitPolicyVm1 = (@{
          id="/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME"
        ports=(@{
           number=22;
           endTimeUtc="2018-09-17T17:00:00.3658798Z";
           allowedSourceAddressPrefix=@("IPV4ADDRESS")})})
2.  Helyezze be a virtuális gép hozzáférési kérelmének paramétereit egy tömbbe:

        $JitPolicyArr=@($JitPolicyVm1)
3.  A kérelem elérésének elküldése (az 1. lépésben kapott erőforrás-azonosító használata)

        Start-AzJitNetworkAccessPolicy -ResourceId "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Security/locations/LOCATION/jitNetworkAccessPolicies/default" -VirtualMachine $JitPolicyArr

További információt a PowerShell-parancsmag dokumentációjában talál.

## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta, hogyan férhet hozzá az Azure-beli virtuális gépekhez való hozzáféréshez, Security Center az igény szerinti virtuálisgép-hozzáférés szabályozását.

A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

- [Biztonsági házirendek beállítása](tutorial-security-policy.md) – Ismerje meg, hogyan konfigurálhatja az Azure-előfizetések és-erőforráscsoportok biztonsági szabályzatait.
- [Biztonsági javaslatok kezelése](security-center-recommendations.md) – Ismerje meg, hogy a javaslatok Hogyan segítenek az Azure-erőforrások védelmében.
- [Biztonsági állapot figyelése](security-center-monitoring.md) – Ismerje meg az Azure-erőforrások állapotának figyelését.
- [Biztonsági riasztások kezelése és válaszadás](security-center-managing-and-responding-alerts.md) – útmutató a biztonsági riasztások kezeléséhez és megválaszolásához.
- [Partneri megoldások monitorozása](security-center-partner-solutions.md) – Ismerje meg, hogyan figyelheti meg a partneri megoldások állapotát.
- [Security Center GYIK](security-center-faq.md) – gyakori kérdések a szolgáltatás használatával kapcsolatban.
- [Azure Security blog](https://blogs.msdn.microsoft.com/azuresecurity/) – Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.

