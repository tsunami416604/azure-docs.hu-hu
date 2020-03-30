---
title: Just-in-time virtuális gép hozzáférés az Azure Security Centerben | Microsoft dokumentumok
description: Ez a dokumentum bemutatja, hogy az Azure Security Center ben a virtuális gépek hez való just-in-time virtuális gép-hozzáférés hogyan segíti az Azure virtuális gépekhez való hozzáférés szabályozását.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: 4b2b388fb736997010a6cbbdf93b23b77c7ef3a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77603970"
---
# <a name="secure-your-management-ports-with-just-in-time-access"></a>Biztosítsa felügyeleti portjait a just-in-time hozzáféréssel

Ha a Security Center szokásos tarifacsomagját (lásd: [díjszabási)](/azure/security-center/security-center-pricing)rendelkezik, az Azure-beli virtuális gépekhez érkező bejövő forgalmat a just-in-time (JIT) virtuális gép (VM) elérésével zárolhatja. Ez csökkenti a támadásoknak való kitettséget, miközben szükség esetén könnyű hozzáférést biztosít a virtuális gépekhez való csatlakozáshoz.

> [!NOTE]
> Security Center just-in-time virtuális gép-hozzáférés jelenleg csak az Azure Resource Manager en keresztül telepített virtuális gépeket támogatja. Ha többet szeretne megtudni a klasszikus és a Resource Manager üzembe helyezési modellekről, olvassa el az [Azure Resource Manager és a klasszikus üzembe helyezés című témakört.](../azure-resource-manager/management/deployment-models.md)

[!INCLUDE [security-center-jit-description](../../includes/security-center-jit-description.md)]

## <a name="configure-jit-on-a-vm"></a>JIT konfigurálása virtuális gépen

A virtuális gép en háromféleképpen konfigurálhat jit-házirendet:

- [JIT-hozzáférés konfigurálása az Azure Security Centerben](#jit-asc)
- [JIT-hozzáférés konfigurálása egy Azure virtuális gép oldalán](#jit-vm)
- [JIT-házirend konfigurálása virtuális számítógépen programozott módon](#jit-program)

## <a name="configure-jit-in-azure-security-center"></a>JIT konfigurálása az Azure Security Centerben

A Security Centerből konfigurálhat egy JIT-házirendet, és hozzáférést kérhet egy virtuális géphez egy JIT-házirend használatával

### <a name="configure-jit-access-on-a-vm-in-security-center"></a>JIT-hozzáférés konfigurálása virtuális gépen a Security Centerben<a name="jit-asc"></a>

1. Nyissa meg a **Security Center** irányítópultját.

1. A bal oldali ablaktáblában válassza **a Just-in-time virtuális gép-hozzáférés**lehetőséget.

    ![Just-in-time virtuális gép hozzáférési csempéje](./media/security-center-just-in-time/just-in-time.png)

    Megnyílik **a Just-in-time virtuális gép hozzáférési** ablaka, és a virtuális gépek állapotára vonatkozó információkat jelenít meg:

    - **Konfigurálva** – virtuális gépek, amelyek úgy vannak konfigurálva, hogy támogassák a virtuális gép elérését. A bemutatott adatok az elmúlt héten, és tartalmazza az egyes virtuális gépek a jóváhagyott kérelmek száma, az utolsó hozzáférési dátum és idő, és az utolsó felhasználó.
    - **Ajánlott** – virtuális gépek, amelyek támogatják a virtuális gép-hozzáférést, de még nincs konfigurálva. Azt javasoljuk, hogy engedélyezze a virtuális gépek csak időben virtuális gép hozzáférés-vezérlését.
    - **No recommendation** (Nincs javaslat) – A virtuális gépek a következő okokból kerülhetnek ebbe a kategóriába:
      - Hiányzó NSG - A just-in-time megoldás hoz egy NSG kell a helyén.
      - Klasszikus virtuális gép – Security Center just-in-time virtuális gép-hozzáférés jelenleg csak az Azure Resource Manager en keresztül telepített virtuális gépeket támogatja. A klasszikus üzembe helyezést nem támogatja a just-in-time megoldás. 
      - Egyéb – A virtuális gép ebben a kategóriában, ha a just-in-time megoldás ki van kapcsolva az előfizetés vagy az erőforrás-csoport biztonsági házirendje, vagy ha a virtuális gép hiányzik egy nyilvános IP-cím, és nem rendelkezik nsg-vel.

1. Válassza az **Ajánlott** lapot.

1. A **VIRTUÁLIS GÉP**csoportban kattintson az engedélyezni kívánt virtuális gépekre. Ezzel egy pipa mellett egy virtuális gép.

      ![Just-in-time hozzáférés engedélyezése](./media/security-center-just-in-time/enable-just-in-time.png)

1. Kattintson **a JIT engedélyezése a virtuális gépeken**elemre. Megnyílik egy ablaktábla, amely az Azure Security Center által ajánlott alapértelmezett portokat jeleníti meg:
    - 22 - SSH
    - 3389 - RDP
    - 5985 - WinRM között 
    - 5986 - WinRM között
1. Tetszés szerint egyéni portokat is hozzáadhat a listához:

      1. Kattintson a **Hozzáadás** gombra. Megnyílik **a Port hozzáadása konfigurációs** ablak.
      1. Minden port, amelyet konfigurál, mind az alapértelmezett, mind az egyéni, testreszabhatja a következő beállításokat:
            - **Protokolltípus**– Az a protokoll, amely a kérelem jóváhagyásakor engedélyezett ezen a porton.
            - **Engedélyezett forrás IP-címek**– Azok az IP-tartományok, amelyek a kérelem jóváhagyásakor engedélyezettek ezen a porton.
            - **Maximális igénylési idő**– Az a maximális időtartam, amely alatt egy adott port megnyitható.

     1. Kattintson az **OK** gombra.

1. Kattintson a **Mentés** gombra.

> [!NOTE]
>Ha a JIT virtuálisgép-hozzáférés engedélyezve van egy virtuális géphez, az Azure Security Center "az összes bejövő forgalom megtagadása" szabályt hoz létre a hálózati biztonsági csoportok és az Azure Firewall kijelölt portjaihoz. Ha más szabályok at hoztak létre a kiválasztott portokhoz, akkor a meglévő szabályok elsőbbséget élveznek az új "minden bejövő forgalom megtagadása" szabályokkal szemben. Ha a kijelölt portokon nincsenek meglévő szabályok, akkor az új "minden bejövő forgalom megtagadása" szabályok elsőbbséget élveznek a hálózati biztonsági csoportok ban és az Azure tűzfalon.


## <a name="request-jit-access-via-security-center"></a>JIT-hozzáférés kérése a Security Centeren keresztül

Hozzáférés kérése a virtuális géphez a Biztonsági központon keresztül:

1. A **Just-in-time virtuális gép-hozzáférés csoportban**válassza a **Konfigurált** lapot.

1. A **Virtuális gép**csoportban kattintson azon virtuális gépekre, amelyekhez hozzáférést szeretne kérni. Ezzel egy pipa a virtuális gép mellett.

    - A Kapcsolat **részletei** oszlopban lévő ikon jelzi, hogy a jit engedélyezve van-e az NSG-n vagy az FW-n. Ha mindkettőn engedélyezve van, csak a Tűzfal ikon jelenik meg.

    - A **Kapcsolat részletei** oszlop a virtuális gép és a megnyitott portok csatlakoztatásához szükséges információkat tartalmazza.

      ![Igény szerinti hozzáférés igénylése](./media/security-center-just-in-time/request-just-in-time-access.png)

1. Kattintson **a Hozzáférés kérése gombra.** Megnyílik **a Hozzáférés kérése** ablak.

      ![JIT részletek](./media/security-center-just-in-time/just-in-time-details.png)

1. A **Hozzáférés kérése**csoportban minden virtuális géphez konfigurálja a megnyitni kívánt portokat és a forrás IP-címeket, amelyeken a port meg van nyitva, valamint azt az időablakot, amelyen a port meg lesz nyitva. Csak a just-in-time házirendben konfigurált portokhoz lehet hozzáférést kérni. Minden port rendelkezik a just-in-time házirendből származó maximális engedélyezett idővel.

1. Kattintson **a Portok megnyitása gombra.**

> [!NOTE]
> Ha egy hozzáférést kérő felhasználó egy proxy mögött van, előfordulhat, hogy a **Saját IP-cím** lehetőség nem működik. Előfordulhat, hogy meg kell határoznia a szervezet teljes IP-címtartományát.



## <a name="edit-a-jit-access-policy-via-security-center"></a>JIT hozzáférési házirend szerkesztése a Security Center en keresztül

Módosíthatja a virtuális gép meglévő just-in-time szabályzat hozzáadásával és konfigurálásával egy új portot, hogy a virtuális gép, vagy ha módosítja a már védett porthoz kapcsolódó bármely más beállítást.

A virtuális gép meglévő just-in-time házirendjének szerkesztése:

1. A **Konfigurált** lap **Virtuális gépek**csoportjában válassza ki azt a virtuális gépet, amelyhez portot szeretne hozzáadni, kattintson az adott virtuális gép sorában lévő három pontra. 

1. Válassza a **Szerkesztés** elemet.

1. A **JIT virtuálisgép-hozzáférési konfiguráció**alatt szerkesztheti egy már védett port meglévő beállításait, vagy új egyéni portot adhat hozzá. 
  ![jit vm hozzáférés](./media/security-center-just-in-time/edit-policy.png)



## <a name="audit-jit-access-activity-in-security-center"></a>JIT-hozzáférési tevékenység naplózása a Security Centerben

A naplókeresés segítségével betekintést nyerhet a virtuális gép tevékenységeibe. Naplók megtekintése:

1. A **Just-in-time virtuális gép-hozzáférés csoportban**válassza a **Konfigurált** lapot.
2. A **virtuális gépek**csoportban válassza ki azt a virtuális gépet, amelyről az információk megtekintéséhez kattintson az adott virtuális gép sorában lévő három pontra, és válassza a menü **Tevékenységnapló parancsát.** Megnyílik **a tevékenységnapló.**

   ![Tevékenységnapló kiválasztása](./media/security-center-just-in-time/select-activity-log.png)

   **A tevékenységnapló** az adott virtuális gép korábbi műveleteinek szűrt nézetét, valamint az időt, a dátumot és az előfizetést tartalmazza.

Letöltheti a napló információkat kiválasztásával **Kattintson ide, hogy töltse le az összes elemet CSV**.

Módosítsa a szűrőket, és kattintson az **Alkalmaz** gombra a keresés és napló létrehozásához.



## <a name="configure-jit-access-from-an-azure-vms-page"></a>JIT-hozzáférés konfigurálása az Azure virtuális gép lapjáról<a name="jit-vm"></a>

Az Ön kényelme érdekében csatlakozhat egy virtuális gép hez a JIT használatával közvetlenül a virtuális gép a Biztonsági központ lapján.

### <a name="configure-jit-access-on-a-vm-via-the-azure-vm-page"></a>JIT-hozzáférés konfigurálása virtuális gépen az Azure virtuális gép lapján keresztül

Annak érdekében, hogy megkönnyítse a virtuális gépek en belüli csak időben történő hozzáférés bevezetését, beállíthatja a virtuális gépet, hogy csak a virtuális gépen belül csak az időben történő hozzáférést engedélyezze.

1. Az [Azure Portalon](https://ms.portal.azure.com)keresse meg és válassza ki a **Virtuális gépek**lehetőséget. 
2. Válassza ki azt a virtuális gépet, amelyet csak az időben szeretne elérni.
3. A menüben válassza a **Konfiguráció parancsot.**
4. A **Just-in-time access (Just-in-time access) csoportban**válassza **a Just-in-time**lehetőséget. 

Ez lehetővé teszi a virtuális gép just-in-time hozzáférést a következő beállításokkal:

- Windows-kiszolgálók:
    - RDP-port 3389
    - Három órányi maximális hozzáférés
    - Az engedélyezett forrás IP-címek beállítása Bármely
- Linux szerverek:
    - SSH port 22
    - Három órányi maximális hozzáférés
    - Az engedélyezett forrás IP-címek beállítása Bármely
     
Ha egy virtuális gép már rendelkezik just-in-time engedélyezve van, amikor megnyitja a konfigurációs lap látni fogja, hogy just-in-time engedélyezve van, és a hivatkozás segítségével nyissa meg a szabályzatot az Azure Security Center ben a beállítások megtekintéséhez és módosításához.

![jit config a vm](./media/security-center-just-in-time/jit-vm-config.png)

### <a name="request-jit-access-to-a-vm-via-an-azure-vms-page"></a>JIT-hozzáférés kérése egy virtuális géphez egy Azure virtuális gép oldalán keresztül

Az Azure Portalon, amikor megpróbál csatlakozni egy virtuális géphez, az Azure ellenőrzi, hogy van-e just-in-time hozzáférési szabályzat konfigurálva az adott virtuális gép.

- Ha a virtuális gépen van konfigurálva egy JIT-házirend, a virtuális gép jit-házirendjének megfelelően a **Hozzáférés kérése** a hozzáférés engedélyezéséhez lehetőségre kattintva. 

  >![jit kérelem](./media/security-center-just-in-time/jit-request.png)

  A hozzáférés a következő alapértelmezett paraméterekkel történik:

  - **forrás IP**: "Any" (*) (nem módosítható)
  - **időtartomány**: Három óra (nem módosítható) <!--Isn't this set in the policy-->
  - **port száma** RDP port 3389 For Windows / port 22 Linux (lehet változtatni)

    > [!NOTE]
    > Miután jóváhagyta a kérelmet az Azure Firewall által védett virtuális gép, a Security Center biztosítja a felhasználó számára a megfelelő kapcsolat részleteit (a dnst-tábla portleképezése) a virtuális géphez való csatlakozáshoz.

- Ha nincs konfigurálva jit egy virtuális gépen, a rendszer kéri, hogy konfigurálja a JIT-házirend rajta.

  ![jit kérdés](./media/security-center-just-in-time/jit-prompt.png)

## <a name="configure-a-jit-policy-on-a-vm-programmatically"></a>JIT-házirend konfigurálása virtuális számítógépen programozott módon<a name="jit-program"></a>

Beállíthatja és használhatja a REST API-kon keresztül és a PowerShell en keresztül just-in-time.

### <a name="jit-vm-access-via-rest-apis"></a>JIT virtuális gép hozzáférés REST API-kon keresztül

A just-in-time virtuális gép hozzáférési funkció az Azure Security Center API-n keresztül használható. Ezen API-n keresztül információkat kaphat a konfigurált virtuális gépekről, újakat adhat hozzá, hozzáférést kérhet egy virtuális géphez, és egyebeket is kaphat. További információ a Közvetlen időben elérhető REST API-ról a [Jit network access házirendjei](https://docs.microsoft.com/rest/api/securitycenter/jitnetworkaccesspolicies)című témakörben található.

### <a name="jit-vm-access-via-powershell"></a>JIT virtuális gép elérése a PowerShellen keresztül

A just-in-time virtuálisgép-hozzáférési megoldás powershellen keresztül, használja a hivatalos Azure Security `Set-AzJitNetworkAccessPolicy`Center PowerShell-parancsmagok, és különösen.

A következő példa beállítja a just-in-time virtuális gép hozzáférési szabályzategy adott virtuális gép, és a következőket állítja be:

1.  Zárja be a 22-es és a 3389-es portot.

2.  Állítson be egy 3 órás maximális időablakot, hogy jóváhagyott kérésenként meglehessen nyitni őket.
3.  Lehetővé teszi, hogy a hozzáférést kérő felhasználó szabályozhatja a forrás IP-címeket, és lehetővé teszi, hogy a felhasználó sikeres munkamenetet hozzon létre egy jóváhagyott just-in-time hozzáférési kérelem esetén.

A powershellben a következőket futtassa a következők megvalósításához:

1.  Rendeljen hozzá egy változót, amely tartalmazza a virtuális gép just-in-time virtuálisgép-hozzáférési szabályzatát:

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

2.  Helyezze be a virtuális gép just-in-time virtuálisgép-hozzáférési szabályzatot egy tömbbe:
    
        $JitPolicyArr=@($JitPolicy)

3.  Konfigurálja a just-in-time virtuálisgép-hozzáférési szabályzatot a kiválasztott virtuális gépen:
    
        Set-AzJitNetworkAccessPolicy -Kind "Basic" -Location "LOCATION" -Name "default" -ResourceGroupName "RESOURCEGROUP" -VirtualMachine $JitPolicyArr 

### <a name="request-access-to-a-vm-via-powershell"></a>Hozzáférés kérése a virtuális géphez a PowerShellen keresztül

A következő példában egy just-in-time virtuális gép hozzáférési kérelmet láthat egy adott virtuális géphez, amelyben a 22-es portot meg kell nyitni egy adott IP-címhez és egy adott ideig:

Futtassa a következőket a PowerShellben:
1.  A virtuális gép kérelem-hozzáférési tulajdonságainak konfigurálása

        $JitPolicyVm1 = (@{
          id="/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME"
        ports=(@{
           number=22;
           endTimeUtc="2018-09-17T17:00:00.3658798Z";
           allowedSourceAddressPrefix=@("IPV4ADDRESS")})})
2.  Szúrja be a virtuális gép hozzáférési kérelem paramétereit egy tömbbe:

        $JitPolicyArr=@($JitPolicyVm1)
3.  A kérelem hozzáférésének küldése (az 1. lépésben kapott erőforrás-azonosító használata)

        Start-AzJitNetworkAccessPolicy -ResourceId "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Security/locations/LOCATION/jitNetworkAccessPolicies/default" -VirtualMachine $JitPolicyArr

További információt a [PowerShell-parancsmag dokumentációjában](https://docs.microsoft.com/powershell/scripting/developer/cmdlet/cmdlet-overview)talál.


## <a name="automatic-cleanup-of-redundant-jit-rules"></a>Redundáns jit-szabályok automatikus karbantartása 

A jit-házirend frissítésekor a törlési eszköz automatikusan elindul a teljes szabálykészlet érvényességének ellenőrzéséhez. Az eszköz a szabályzatban és az NSG-ben lévő szabályok közötti eltéréseket keresi. Ha a törlési eszköz eltérést talál, meghatározza az okát, és ha ez biztonságos, eltávolítja azokat a beépített szabályokat, amelyekre már nincs szükség. A tisztító soha nem törli a létrehozott szabályokat.

Példák arra, amikor a tisztító eltávolíthat egy beépített szabályt:

- Ha két azonos definíciós szabály létezik, és az egyiknek magasabb prioritása van, mint a másiknak (ami azt jelenti, hogy az alacsonyabb prioritású szabály soha nem lesz használva)
- Ha a szabály leírása tartalmazza egy virtuális gép nevét, amely nem felel meg a cél IP-címnek a szabályban 


## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan csak-in-time virtuális gép-hozzáférés a Security Center segítségével szabályozhatja az Azure virtuális gépekhez való hozzáférés.

A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

- [Biztonsági házirendek beállítása](tutorial-security-policy.md) – Megtudhatja, hogyan konfigurálhatja a biztonsági szabályzatokat az Azure-előfizetésekhez és erőforráscsoportokhoz.
- [Biztonsági javaslatok kezelése](security-center-recommendations.md) – Ismerje meg, hogyan segíthetnek a javaslatok az Azure-erőforrások védelmében.
- [Biztonsági állapotfigyelés](security-center-monitoring.md) – Ismerje meg, hogyan figyelheti az Azure-erőforrások állapotát.