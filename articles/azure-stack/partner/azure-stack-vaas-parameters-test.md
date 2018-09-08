---
title: Teszt paramétereiben a szolgáltatás az Azure Stack érvényesítési |} A Microsoft Docs
description: A referencia-témakör a konfigurációs fájlban és a teszt paraméterekkel érvényesítéshez szolgáltatásként az Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 9f042779e3463f0d75dc6327b3553156edbf162a
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44162223"
---
# <a name="test-parameters-for-validation-as-a-service-azure-stack"></a>Teszt paramétereiben szolgáltatásként az Azure Stack-érvényesítéshez

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Mielőtt hajtana végre bármely tesztelése Suite ellenőrzések szolgáltatásként (VaaS), válasszon ki egy megoldást, és hozzon létre egy tesztmenetek.

- Jelentkezzen be a regisztrált VaaS bérlői hitelesítő adataival.
- Hozzon létre egy új megoldást (kiválasztásával **megoldás hozzáadása**), vagy válasszon egy meglévő.
- Válassza ki a **Start** gombra a **teszt pass** munkafolyamat csempe.

> [!Note]  
> Hozzon létre egy új megoldás minden egyes egyedi gép set-hardver-konfigurációjához, amely már érvényesítése, és az adott gép készlet minden build üzembe helyezéshez továbbíthatja egy új teszt.

Adja meg a tesztek futtatásához szükséges paramétereket kell a **át tesztadatokat** lapot. Adja meg ezeket a paramétereket, amikor egy új tesztmenetek vagy ellenőrzési futtatás kezdési. A paraméterek a legtöbb Azure Stack üzembe helyezésekor megadott ugyanazzal az értékkel rendelkeznek.

A manuális szereplő értékeket adhat meg a [teszt paramétereket tartalmazó](#test-parameters), vagy töltse fel a telepítési konfigurációs fájlt, amely tartalmazza a teljes Azure Stack stamp. Feltöltése után a portálon betölti az értékeket a fájlból.

> [!Note]  
> Lehetőség van a keresése, és írja be a teszt paraméterértékek megkeresésével és a konfigurációs fájl betöltése be a portálra.

## <a name="export-the-test-parameters-using-powershell"></a>A PowerShell-lel tesztparaméterekre exportálása

1. Jelentkezzen be DVM vagy bármely olyan gépen, amely hozzáfér az Azure Stack-környezet.
2. Nyisson meg egy rendszergazda jogú PowerShell-ablakot, és futtassa:

    ````PowerShell  
        $params = Invoke-RestMethod -Method Get -Uri 'https://ASAppGateway:4443/ServiceTypeId/4dde37cc-6ee0-4d75-9444-7061e156507f/CloudDefinition/GetStampInformation'
        ConvertTo-Json $params > stampinfoproperties.json
    ````

3. Töltse fel **stampinfoproperties.json** a VaaS portálra.

## <a name="find-the-test-parameters-in-the-configuration-file"></a>A konfigurációs fájlban keresse meg a teszt paraméterekkel

Is megtalálhatja a teszt paraméterértékek nyissa meg a Dokumentumokat **konfigurációs fájl**. A fájl elérési útja a következő a DVM gépen található:  
`C:\EceStore\403314e1-d945-9558-fad2-42ba21985248\80e0921f-56b5-17d3-29f5-cd41bf862787`

## <a name="test-parameters"></a>Vizsgálati paraméter 

| Paraméter    | Leírás |
|-------------|-----------------|
| Azure Stack Build                      | Az Azure Stack hozhat létre, vagy a verzió számát, korábban telepített például 1.0.170330.0 | 
| Bérlőazonosító                              | Az Azure Active Directory-bérlő az Azure Stack üzembe helyezése során megadott. Keresse meg **AADTenant** a konfigurációs fájlban, és válassza ki a **GUID** értékét a `Id` címke. | 
| Régió                                 | Azure Stack-telepítési régió. Keresse meg **régió** a konfigurációs fájlban. | 
| Bérlői erőforrás-kezelő                | A bérlői Azure Resource Manager-végpontot, például `https://management.<ExternalFqdn>` | 
| Rendszergazdai erőforrás-kezelő                 | Rendszergazdai Azure Resource Manager-végpontot. Ha például `https://adminmanagement.<ExternalFqdn>` | 
| Külső teljes Tartományneve                          | A környezet külső teljes Tartományneve. Keresse meg **ExternalFqdn** a konfigurációs fájlban. | 
| Bérlői felhasználó                            | Az Azure Active Directory-bérlői rendszergazdai vagy már üzembe helyezett vagy ki kell építeni az Azure AD-címtárban a szolgáltatás-rendszergazda által igényeinek megfelelően. Részleteket a bérlői fiók kiépítése [adjon hozzá egy új Azure Stack-bérlői fiókkal az Azure Active Directoryban](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-new-user-aad). Ezt az értéket a teszt által használt erőforrások kiépítése sablonok üzembe helyezése például a bérlői szintű műveletek végrehajtásához (virtuális gép, storage-fiókok stb) és a számítási feladatokat. | 
| Szolgáltatás-rendszergazda felhasználó             | Az Azure Active Directory-rendszergazda az Azure Stack üzembe helyezése során megadott Azure Active Directory-bérlő. Keresse meg **AADTenant** található a konfigurációs fájlt, és válassza ki az értéket a `UniqueName` címke a konfigurációs fájlban. | 

## <a name="checks-before-starting-the-tests"></a>A vizsgálatok elindítása előtt ellenőrzi

A tesztek távoli műveletek végrehajtása. A gép, amely futtatja a teszteket az Azure Stack-végpontokra irányuló hozzáféréssel kell rendelkeznie. Ellenkező esetben a teszt nem fog működni. Ha a VaaS helyi ügynökön használ, használja a gép ahol az ügynök futni fog. Ellenőrizheti, hogy a gép rendelkezik-e az alábbi ellenőrzéseket futtatja, az Azure Stack pontok eléréséhez.

1. Ellenőrizze, hogy az alap URI-t érhető el. Nyisson meg egy parancssort vagy a bash rendszerhéjat, és futtassa a következő parancsot:

    ```bash  
    nslookup adminmanagement.<EXTERNALFQDN>
    ```

2. Nyisson meg egy webböngészőt, és navigáljon a `https://adminportal.<EXTERNALFQDN>` annak érdekében, hogy ellenőrizze, hogy a MAS-portálon érhető el.

3. Jelentkezzen be az Azure ad szolgáltatás a tesztelési fázisban létrehozásakor megadott rendszergazdai és a jelszó értékét.

## <a name="next-steps"></a>További lépések

- Tudjon meg többet a [szolgáltatásként az Azure Stack érvényesítési](https://docs.microsoft.com/azure/azure-stack/partner).
