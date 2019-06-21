---
title: Hozzáférés a Just-in-time virtuális gépek az Azure Security Centerben |} A Microsoft Docs
description: Ez a dokumentum bemutatja, hogyan just-in-time Virtuálisgép-hozzáférés az Azure Security Center segítségével szabályozhatja a hozzáférést az Azure-beli virtuális gépek.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 671930b1-fc84-4ae2-bf7c-d34ea37ec5c7
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 6/17/2019
ms.author: v-mohabe
ms.openlocfilehash: eb9366acf82c94bdf99c4d4f0c7c6bdf4f51e06d
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67295013"
---
# <a name="manage-virtual-machine-access-using-just-in-time"></a>Just-in-time virtuálisgép-hozzáférés kezelése

– Igény (szerinti JIT) virtuális gép (VM) hozzáférés segítségével zárolását, így az Azure virtuális gépekre, csökkentve a támadásokkal szembeni sérülékenységet ugyanakkor könnyű hozzáférést biztosít arra, hogy szükség esetén a virtuális gépekhez csatlakozhat a bejövő forgalmat.

> [!NOTE]
> A just-in-time funkció a Security Center Standard szinten érhető el.  A Security Center tarifacsomagjaival kapcsolatos további információért lásd a [díjszabást](security-center-pricing.md).


> [!NOTE]
> A Security Center – igény virtuális gép eléréséhez jelenleg támogatja a csak a virtuális gépet üzembe helyezni az Azure Resource Manageren keresztül. Ismerje meg, a klasszikus és Resource Manager üzembe helyezési modellel kapcsolatos információkért tekintse meg a [Azure Resource Manager és klasszikus üzembe helyezési](../azure-resource-manager/resource-manager-deployment-model.md).

## <a name="attack-scenario"></a>Támadás

Találgatásos támadások, gyakran cél felügyeleti portokhoz való hozzáférést egy virtuális géphez. Sikeres, ha egy támadó irányítását a virtuális gép felett, és egy foothold létesíteni a saját környezetben.

Találgatásos támadás való kitettség csökkentése érdekében egyik módja, hogy korlátozza, ameddig egy port nyitva. A felügyeleti portoknak nem kell mindig nyitva lenniük. Csak addig kell nyitva lenniük, amíg Ön csatlakozik a virtuális géphez, például azért, hogy felügyeleti vagy karbantartási feladatokat végezzen. Amikor a just-in-time engedélyezve van, a Security Center az [hálózati biztonsági csoport](../virtual-network/security-overview.md#security-rules) (NSG) és az Azure tűzfal-szabályok, amelyek a felügyeleti portokhoz való hozzáférés korlátozása, így a támadók tudják célba.

![Just-in-time-forgatókönyv](./media/security-center-just-in-time/just-in-time-scenario.png)

## <a name="how-does-jit-access-work"></a>Hogyan működik a JIT hozzáférési?

Ha just-in-time engedélyezve van, a Security Center érkező forgalmat zárol az Azure-beli virtuális gépek egy NSG-szabály létrehozásával. Kiválaszthatja a portokat a virtuális gépen, amelyre a bejövő forgalom lesz zárolva. Ezeket a portokat a just-in-time-megoldás szabályozza.

Amikor egy felhasználó egy virtuális Géphez való hozzáférés, a Security Center ellenőrzi, hogy a felhasználó rendelkezik-e [szerepköralapú hozzáférés-vezérlés (RBAC)](../role-based-access-control/role-assignments-portal.md) engedélyeket, lehetővé teszi az sikeresen igényelhet hozzáférést egy virtuális Gépet. Ha a kérelmet jóváhagyták, a Security Center automatikusan konfigurálja a hálózati biztonsági csoportok (NSG-k) és az Azure tűzfal a kiválasztott portot és kért forrás IP-címek vagy tartományok, ennyi ideig megadott bejövő forgalom engedélyezésére. Az időszak lejárta után a Security Center visszaállítja az NSG-k korábbi állapotába. Ezeket a kapcsolatokat korábban már létrehozott nem folyamatban szakadnak meg, azonban.

 > [!NOTE]
 > A JIT hozzáférési kérelem jóváhagyása egy virtuális gép, egy Azure-tűzfal mögött található, majd a Security Center automatikusan módosítja az NSG-t és a tűzfal-szabályok. Átvihető megadott idő a szabályok a kiválasztott portot és a kért forrás IP-címek vagy tartományok beérkező forgalom engedélyezésére. Az idő után a Security Center visszaállítja a tűzfallal és NSG-szabályok a korábbi állapotába.


## <a name="permissions-needed-to-configure-and-use-jit"></a>Konfigurálhatja és használhatja az igény szerinti szükséges engedélyek

| A felhasználó engedélyezése: | Engedélyek beállítása|
| --- | --- |
| Konfigurálja, vagy egy virtuális gép egy igény szerinti szabályzat szerkesztése | *Ezeket a műveleteket a szerepkör hozzárendelése:*  Az egy előfizetésen vagy erőforráscsoporton hatóköre a virtuális gép társítva: ```Microsoft.Security/locations/jitNetworkAccessPolicies/write``` Az egy előfizetést vagy erőforráscsoportot, vagy a virtuális gép hatóköre: ```Microsoft.Compute/virtualMachines/write``` | 
| ||
|Virtuális gépek igény szerinti hozzáférés kérése | *Ezek a műveletek hozzárendelése a felhasználóhoz:*  Az egy előfizetésen vagy erőforráscsoporton hatóköre a virtuális gép társítva:  ```Microsoft.Security/locations/{the_location_of_the_VM}/jitNetworkAccessPolicies/initiate/action``` Az egy előfizetést vagy erőforráscsoportot, vagy a virtuális gép hatóköre: ```Microsoft.Compute/virtualMachines/read``` |


## <a name="configure-jit-on-a-vm"></a>A virtuális gép JIT konfigurálása

A virtuális gép egy igény szerinti szabályzat konfigurálásához 3 módja van:

- [Igény szerinti elérésének konfigurálása az Azure Security Centerben](#jit-asc)
- [Igény szerinti elérésének konfigurálása az egy Azure virtuális gép paneljén](#jit-vm)
- [Igény szerinti szabályzat programozott módon a virtuális gép konfigurálása](#jit-program)

## <a name="configure-jit-in-asc"></a>Az ASC igény szerinti konfigurálása

Az ASC-ről konfigurálhatja egy igény szerinti szabályzat és a kérelem egy virtuális Géphez való hozzáférés igény szerinti házirend segítségével


### Egy virtuális gépen az ASC igény szerinti elérésének konfigurálása <a name="jit-asc"></a>

1. Nyissa meg a **Security Center** irányítópultját.

2. A bal oldali panelen válassza ki a **Just in time VM access**.

    ![Just-in-time VM access csempe](./media/security-center-just-in-time/just-in-time.png)

    A **Just in time VM access** ablak nyílik meg.

      ![Igény szerinti elérésének engedélyezése](./media/security-center-just-in-time/enable-just-in-time.png)

    **Just-in-time VM access** információt nyújt a virtuális gépek állapotát:

    - **Konfigurált** -just-in-time VM access támogatására konfigurált virtuális gépek. Az adatok a múlt hétre vonatkoznak, és az egyes virtuális Gépekhez tartalmazza a jóváhagyott kérések, a legutóbbi hozzáférés dátuma és ideje és utolsó felhasználó számát.
    - **Ajánlott** – virtuális gépek igény szerinti Virtuálisgép-hozzáférési is támogató, de az nem lett konfigurálva. Azt javasoljuk, hogy engedélyezze ezeket a virtuális gépek just-in-time VM hozzáférés-vezérlés. 
    - **No recommendation** (Nincs javaslat) – A virtuális gépek a következő okokból kerülhetnek ebbe a kategóriába:
      - Hiányzó NSG - a-igény megoldás szükség van egy NSG helyén lennie.
      - Klasszikus virtuális gép – Security Center just-in-time VM access csak virtuális gépek Azure Resource Managerrel üzembe helyezett jelenleg támogatja. Klasszikus üzemi modellben a just-in-time-megoldás által nem támogatott. 
      - Egyéb - egy virtuális Gépet az ebbe a kategóriába, ha a just-in-time-megoldás ki van kapcsolva, a biztonsági szabályzatban, az előfizetés vagy az erőforráscsoportot, vagy ha a virtuális gép nyilvános IP-cím hiányzik, és nem rendelkezik NSG helyen.

3. Válassza ki a **ajánlott** fülre.

4. A **virtuális gép**, kattintson az engedélyezni kívánt virtuális gépeket. Ez a virtuális gép melletti jelölőnégyzet bejelölésekor helyezi.

5. Kattintson a **engedélyezése a virtuális gépek igény szerinti**.
   -. Ezen a panelen jelenik meg az Azure Security Center által ajánlott alapértelmezett portokat:
      - 22 - SSH
      - 3389 - RDP
      - 5985 - Rendszerfelügyeleti webszolgáltatások 
      - 5986-os – Rendszerfelügyeleti webszolgáltatások
6. Konfigurálhat egyéni portokat is:

      1. Kattintson a **Hozzáadás**lehetőségre. A **Add port configuration** ablak nyílik meg.
      2. Minden egyes porthoz úgy dönt, hogy adja meg, mindkét alapértelmezett és egyéni, testre szabhatja a következő beállításokat:

    - **Protokoll típusa**– ezen a porton van engedélyezve, ha a kérelem jóváhagyása a protokollt.
    - **Engedélyezett forrás IP-címek**– az IP-címtartományokat, amelyek jogosultak ezen a porton, amikor jóváhagyják a kérését.
    - **Kérelem maximális időtartama**– a maximális időtartam, amely során egy adott portot is megnyithatók.

     3. Kattintson az **OK** gombra.

1. Kattintson a **Save** (Mentés) gombra.

> [!NOTE]
>Ha egy virtuális Gépet igény szerinti Virtuálisgép-hozzáférés engedélyezve van, az Azure Security Center szabályokat hoz létre "minden bejövő forgalom Elutasítás" a kiválasztott portok a társított hálózati biztonsági csoportok és az Azure-tűzfal vele. Ha más szabályokat a kiválasztott portok létrehozott, majd a meglévő szabályok prioritást élveznek az új "az összes bejövő forgalmat Elutasítás" szabályok. Ha nincsenek meglévő szabályok a kijelölt porton, az új "az összes bejövő forgalmat Elutasítás" szabályokat a hálózati biztonsági csoportok és az Azure-tűzfal a legmagasabb prioritást igénybe vehet.


## <a name="request-jit-access-via-asc"></a>Igény szerinti hozzáférés kéréséhez ASC-n keresztül

Igényelhet hozzáférést egy virtuális géphez ASC:

1. A **igény szerinti Virtuálisgép-hozzáférési**, jelölje be a **konfigurált** fülre.

2. A **virtuális gép**, kattintson a igényeljen hozzáférést ahhoz a kívánt virtuális gépeket. Ez helyezi a virtuális gép melletti jelölőnégyzet bejelölésekor.


    - Az ikonra a **kapcsolat adatai** oszlop azt jelzi, hogy az igény szerinti engedélyezve van-e az NSG-t vagy Keretrendszer. Ha mindkét engedélyezve van, csak a tűzfal ikon jelenik meg.

    - A **kapcsolat adatai** oszlop tartalmazza a megfelelő információkat a virtuális gép csatlakozik, valamint azt jelzi, hogy a megnyitott portok.

      ![Igény szerinti hozzáférés igénylése](./media/security-center-just-in-time/request-just-in-time-access.png)

3. Kattintson a **hozzáférés kérése**. A **hozzáférés kérése** ablak nyílik meg.

      ![Igény szerinti részletei](./media/security-center-just-in-time/just-in-time-details.png)

4. A **hozzáférés kérése**, az egyes virtuális Gépekhez, konfigurálja a megnyitni kívánt portokat, és a port megnyitását a következőn forrás IP-címek és az időtartomány, amelyre a port lesz megnyitása. Csak lesz lehetséges a just-in-time-házirendben konfigurált portokat hozzáférést kérni. Minden port a maximális engedélyezett idő a just-in-time-házirendből származó rendelkezik.

5. Kattintson a **portok megnyitásához**.

> [!NOTE]
> Ha egy felhasználó hozzáférést kér a proxyt, a lehetőség van-e **saját IP-címet** nem működnek. Szükség lehet a szervezet teljes IP-címtartomány megadása.

## <a name="edit-a-jit-access-policy-via-asc"></a>Igény szerinti hozzáférés a szabályzatot az ASC-n keresztül

Virtuális gép meglévő just-in-time szabályzat hozzáadásával és konfigurálásával a virtuális gép védelmét egy új portot, vagy bármely más beállítás már védett portra kapcsolódó módosításával módosíthatja.

A virtuális gépek meglévő just-in-time-házirend szerkesztése:
1. Az a **konfigurált** lap **virtuális gépek**, válasszon egy virtuális Gépet, amelyre a port hozzáadása a virtuális Gépeket a soron belül három pontra kattintva. 

1. Válassza a **Szerkesztés** elemet.
1. A **virtuális gépek igény szerinti hozzáférés konfigurációs**, szerkesztheti a meglévő beállítások már védett port vagy adjon hozzá egy új egyéni portot. 
  ![igény szerinti virtuálisgép-hozzáférés](./media/security-center-just-in-time/edit-policy.png)

## <a name="audit-jit-access-activity-in-asc"></a>Az ASC JIT hozzáférési tevékenység naplózása

Virtuális gép a tevékenységeket a naplóbeli keresés segítségével betekintést kaphatnak. A naplók megtekintéséhez:

1. A **Just-in-time Virtuálisgép-hozzáférési**, jelölje be a **konfigurált** fülre.
2. A **virtuális gépek**válassza ki a virtuális gép kapcsolatos információk megtekintéséhez kattintson a három pontra a soron belül a virtuális gép, jelölje ki **tevékenységnapló** menüjében. A **tevékenységnapló** nyílik meg.

   ![Válassza ki a tevékenységnaplóban](./media/security-center-just-in-time/select-activity-log.png)

   **Tevékenységnapló** együtt idő, dátum és az előfizetés virtuális gép korábbi műveletek szűrt nézetét jeleníti meg.

Letöltheti a naplózási adatok kiválasztásával **Ide kattintva letöltheti az összes elem CSV-fájlként**.

A szűrőket, és kattintson a **alkalmaz** hozzon létre egy keresési és a napló.



## Igény szerinti elérésének konfigurálása az egy Azure virtuális gép paneljén <a name="jit-vm"></a>

Az Ön kényelme érdekében-alapú virtuális gép JIT közvetlenül a virtuális gép paneljén, az Azure-ban belül csatlakozhat.

### <a name="configure-jit-access-on-a-vm-via-the-azure-vm-blade"></a>Egy virtuális géphez az Azure virtuális gép paneljén a igény szerinti elérésének konfigurálása

Megkönnyíti az igény szerinti hozzáférés bevezetése a virtuális gép között, beállíthatja, hogy csak-igény a hozzáférés közvetlenül a virtuális gép egy virtuális Gépet.

1. Az Azure Portalon válassza ki a **virtuális gépek**.
2. Kattintson a virtuális gépen szeretné korlátozni, igény szerinti elérését.
3. Kattintson a menü **konfigurációs**.
4. A **igény-szerinti hozzáférés** kattintson **just-in-time-házirend engedélyezése**. 

Ez lehetővé teszi igény szerinti hozzáférés a virtuális gép a következő beállításokkal:

- Windows-kiszolgálók:
    - 3389-es RDP-port
    - maximális engedélyezett hozzáférési 3 óra
    - Engedélyezett forrásoldali IP-címeket sem van beállítva.
- Linux-kiszolgálók:
    - 22-es SSH-port
    - maximális engedélyezett hozzáférési 3 óra
    - Engedélyezett forrásoldali IP-címeket sem van beállítva.
     
Ha a virtuális gép már just-in-time engedélyezve van, amikor a konfigurációs lapon tudják, hogy engedélyezve van a-igény és a hivatkozás segítségével nyissa meg a szabályzatot az Azure Security Center használatával megtekintheti és módosíthatja a beállításokat.

![a virtuális gép JIT-config](./media/security-center-just-in-time/jit-vm-config.png)

### <a name="request-jit-access-to-a-vm-via-the-azure-vm-blade"></a>Egy virtuális géphez az Azure virtuális gép paneljén igény szerinti hozzáférés kérése

Az Azure Portalon egy virtuális Géphez való csatlakozáshoz meg az Azure ellenőrzi, hogy ha egy just-in-time hozzáférési szabályzatot, a virtuális gép konfigurálva van.

- Ha rendelkezik egy igény szerinti szabályzat konfigurálva a virtuális gépen, kattinthat **hozzáférés kérése** ahhoz, hogy rendelkezik hozzáféréssel a állítsa be a virtuális gép JIT szabályzatának megfelelően. 

  >![igény szerinti kérelem](./media/security-center-just-in-time/jit-request.png)

  A hozzáférést igényelnek a következő alapértelmezett paraméterek:

  - **forrás IP-cím**: "Bármely" (*) (nem módosítható)
  - **időtartomány**: (nem módosítható) 3 óra  <!--Isn't this set in the policy-->
  - **portszám** RDP a 3389-es port: Windows / 22-es port (módosítható) Linux rendszeren

    > [!NOTE]
    > Miután jóváhagyják a kérését Azure tűzfal által védett virtuális gépek, a Security Center a felhasználó a megfelelő kapcsolati adatokkal (a port hozzárendelése a DNAT-tábla) biztosít a virtuális Géphez való csatlakozáshoz használandó.

- Ha nincs konfigurálva a virtuális gép igény szerinti, akkor egy igény szerinti szabályzat konfigurálásához bekéri azt.

  ![igény szerinti kérése](./media/security-center-just-in-time/jit-prompt.png)

## Igény szerinti szabályzat programozott módon a virtuális gép konfigurálása  <a name="jit-program"></a>

Állítsa be, és használja a-igény és a Powershellen keresztül REST API-kon keresztül.

## <a name="jit-vm-access-via-rest-apis"></a>REST API-kon keresztül a virtuális gépek igény szerinti hozzáférés

A just-in-time VM access szolgáltatás használható az Azure Security Center API-n keresztül. Konfigurált virtuális gépek adatainak lekérése, újakat vehet fel, kérhet hozzáférést egy virtuális géphez, és több, az API-val. Lásd: [Jit hálózati hozzáférési házirendeket](https://docs.microsoft.com/rest/api/securitycenter/jitnetworkaccesspolicies), további információ a just-in-time REST API-t.

## <a name="jit-vm-access-via-powershell"></a>Virtuális gépek igény szerinti hozzáférés a PowerShell-lel

A just-in-time VM access megoldás Powershellen keresztül, használja a hivatalos Azure Security Center PowerShell-parancsmagok, és kifejezetten `Set-AzJitNetworkAccessPolicy`.

Az alábbi példa egy just-in-time VM hozzáférési szabályzatot állít be egy adott virtuális Gépre, és beállítja a következő:

1.  Zárja be a 22-es és a 3389-es portot.

2.  Az egyes 3 óra maximális idő időszak megadása az így is megnyithatók jóváhagyott kérelmenként.
3.  Lehetővé teszi, hogy a forrás IP-címek szabályozhatja a hozzáférést kér, és lehetővé teszi a felhasználó sikeres munkamenetet létrehozni egy jóváhagyott just-in-time-hozzáférési kérés esetén a felhasználó.

Futtassa a következő PowerShell-lel ehhez:

1.  Rendelje hozzá egy változó, amely tartalmazza a just-in-time Virtuálisgép-hozzáférési házirend egy virtuális géphez:

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

2.  Illessze be a virtuális gép just-in-time VM hozzáférési házirend egy tömbre:
    
        $JitPolicyArr=@($JitPolicy)

3.  A just-in-time VM hozzáférési házirendet konfigurálja a kijelölt virtuális gépen:
    
        Set-AzJitNetworkAccessPolicy -Kind "Basic" -Location "LOCATION" -Name "default" -ResourceGroupName "RESOURCEGROUP" -VirtualMachine $JitPolicyArr 

#### <a name="request-access-to-a-vm-via-powershell"></a>Egy virtuális géphez a PowerShell hozzáférés kérése

A következő példában látható egy just-in-time VM hozzáférési kérelmet egy adott virtuális Gépre, a portot kell megnyitni a megadott IP-címet és a egy adott időn 22-es van szükség:

A PowerShellben futtassa a következőt:
1.  A virtuális gép kérelem hozzáférési tulajdonságainak konfigurálása

        $JitPolicyVm1 = (@{
          id="/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME"
        ports=(@{
           number=22;
           endTimeUtc="2018-09-17T17:00:00.3658798Z";
           allowedSourceAddressPrefix=@("IPV4ADDRESS")})})
2.  Helyezze be a virtuális gép hozzáférési kérelem paramétereit a tömböt:

        $JitPolicyArr=@($JitPolicyVm1)
3.  (Használja az erőforrás-azonosító az 1. lépésben kapott) hozzáférési kérelmek küldése

        Start-AzJitNetworkAccessPolicy -ResourceId "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Security/locations/LOCATION/jitNetworkAccessPolicies/default" -VirtualMachine $JitPolicyArr

További információ a PowerShell-parancsmag dokumentációjában talál.

## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta, hogyan just-in-time VM access a Security Center segít a szabályozhatja a hozzáférést az Azure-beli virtuális gépek.

A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

- [Biztonsági szabályzatok beállítása](tutorial-security-policy.md) – ismerje meg, hogyan konfigurálhat biztonsági házirendeket az Azure-előfizetések és -erőforráscsoportok.
- [Biztonsági javaslatok kezelése](security-center-recommendations.md) – megtudhatja, hogyan javaslatok az Azure-erőforrások védelme.
- [Biztonsági állapotfigyelés](security-center-monitoring.md) – útmutató az Azure-erőforrások állapotának monitorozásához.
- [Kezelése és válaszadás a biztonsági riasztások](security-center-managing-and-responding-alerts.md) – ismerje meg, hogyan kezelése és válaszadás a biztonsági riasztásokra.
- [Partneri megoldások monitorozása](security-center-partner-solutions.md) – útmutató a partnermegoldások állapotának monitorozásához.
- [A Security Center – gyakori kérdések](security-center-faq.md) – gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
- [Azure Security blog](https://blogs.msdn.microsoft.com/azuresecurity/) – Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.

