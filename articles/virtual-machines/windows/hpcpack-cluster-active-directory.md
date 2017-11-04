---
title: "Az Azure Active Directoryval HPC Pack fürt |} Microsoft Docs"
description: "Ismerje meg, hogyan integrálható az Azure HPC Pack 2016 fürtöt az Azure Active Directoryval"
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
ms.assetid: 9edf9559-db02-438b-8268-a6cba7b5c8b7
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: big-compute
ms.date: 11/14/2016
ms.author: danlep
ms.openlocfilehash: c5a06a9c810349b1bcce01c7f73563941a5af0ed
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="manage-an-hpc-pack-cluster-in-azure-using-azure-active-directory"></a>Az Azure-ban az Azure Active Directory egy HPC Pack fürt kezelése
[A Microsoft HPC Pack 2016](https://technet.microsoft.com/library/cc514029) való integráció támogatja [Azure Active Directory](../../active-directory/index.md) (az Azure AD) a rendszergazdák, akik HPC Pack-fürt üzembe helyezése az Azure-ban.



Kövesse a cikkben leírt lépéseket a következő magas szintű feladatok: 
* A HPC Pack fürt manuálisan integrálása az Azure AD-bérlő
* Kezelése és az Azure-ban a HPC Pack fürt feladatok ütemezése 

Egy HPC Pack fürtmegoldás integrálása az Azure AD integrálásához egyéb alkalmazások és szolgáltatások normál módon történik. Ez a cikk feltételezi, hogy jártas alapszintű felhasználói kezelése az Azure ad-ben. További információk és a háttérben, tekintse meg a [Azure Active Directory dokumentációjának](../../active-directory/index.md) és a következő szakasz.

## <a name="benefits-of-integration"></a>Integráció előnyei


Azure Active Directory (Azure AD) egy olyan több-bérlős felhőalapú címtár- és identitáskezelési felügyeleti szolgáltatás, amely az egyszeri bejelentkezés (SSO) alapú megoldások hozzáférést biztosít.

Integráció az Azure ad-val HPC Pack fürt segítségére lehetnek a következő célok érdekében:

* A hagyományos Active Directory-tartományvezérlőhöz eltávolítása a HPC Pack fürtről. Ez csökkentheti a költségeket a a fürt karbantartásának, ha ez nem szükséges a vállalatba, illetve a telepítési folyamat számlázhasson.
* Kihasználhatja a következő előnyöket, amely az Azure ad kerülnek:
    *   Egyszeri bejelentkezés 
    *   A helyi AD-azonosítójának használatával a HPC Pack fürthöz az Azure-ban 

    ![Az Azure Active Directory-környezet](./media/hpcpack-cluster-active-directory/aad.png)


## <a name="prerequisites"></a>Előfeltételek
* **Az Azure virtuális gépekre telepített HPC Pack 2016 fürt** - lépéseiért lásd: [HPC Pack 2016-fürt üzembe helyezése az Azure-ban](hpcpack-2016-cluster.md). Az átjárócsomópont DNS-nevét és a végrehajtásához a cikkben egy fürt rendszergazdájának hitelesítő adataira van szüksége.

  > [!NOTE]
  > Az Azure Active Directory-integráció HPC Pack HPC Pack 2016 előtti verzióiban nem támogatott.



* **Ügyfélszámítógép** -HPC Pack ügyfél segédprogramok futtatásához Windows vagy Windows Server ügyfélszámítógépet kell. Ha szeretné feladatok küldéséhez a HPC Pack webes portál vagy a REST API-t használja, minden ügyfélszámítógép az Ön által választott is használhatja.

* **HPC Pack ügyfél segédprogramok** -a HPC Pack ügyfél segédprogramokat telepíthet az ügyfélszámítógépen, a Microsoft Download Center a rendelkezésre álló szabad telepítőcsomag használ.


## <a name="step-1-register-the-hpc-cluster-server-with-your-azure-ad-tenant"></a>1. lépés: Az Azure AD-bérlő regisztrálni a HPC cluster server
1. Jelentkezzen be a [klasszikus Azure portálra](https://manage.windowsazure.com).
2. Kattintson a **Active Directory** a bal oldali menüben, majd kattintson a kívánt könyvtár az előfizetésben. A címtárban található erőforrások hozzáféréssel kell rendelkeznie.
3. Kattintson a **felhasználók**, és győződjön meg arról, hogy a felhasználói fiókok már létrehozott vagy konfigurálva van.
4. Kattintson a **alkalmazások** > **Hozzáadás**, és kattintson a **a szerveztem által fejlesztett alkalmazás hozzáadása**. A varázslóban adja meg a következő információkat:
    * **Név** -HPCPackClusterServer
    * **Típus** – Itt adhatja meg **webes alkalmazáshoz és/vagy webes API-t**
    * **Bejelentkezés URL-cím**-a mintát, amely alapértelmezés szerint az alap URL-cím`https://hpcserver`
    * **App ID URI** - `https://<Directory_name>/<application_name>`. Cserélje le `<Directory_name`> az Azure AD-bérlőn, például teljes nevét `hpclocal.onmicrosoft.com`, és cserélje le `<application_name>` a korábban kiválasztott névvel.

5. Az alkalmazás hozzáadása után kattintson **konfigurálása**. Konfigurálja a következő tulajdonságokat:
    * Válassza ki **Igen** a **alkalmazás több-bérlős**
    * Válassza ki **Igen** a **alkalmazás eléréséhez szükséges felhasználói kiosztása**.

6. Kattintson a **Save** (Mentés) gombra. Mentés befejeztével kattintson **kezelése Manifest**. Ez a művelet az alkalmazás JavaScript object notation (JSON) jegyzékfájl tölti le. A letöltött jegyzékfájl szerkesztésével keresése a `appRoles` beállítása, a következő alkalmazás-szerepkör hozzáadása:
    ```json
    "appRoles": [
        {
        "allowedMemberTypes": [
            "User",
            "Application"
        ],
        "displayName": "HpcAdminMirror",
        "id": "61e10148-16a8-432a-b86d-ef620c3e48ef",
        "isEnabled": true,
        "description": "HpcAdminMirror",
        "value": "HpcAdminMirror"
        },
        {
        "allowedMemberTypes": [
            "User",
            "Application"
        ],
        "description": "HpcUsers",
        "displayName": "HpcUsers",
        "id": "91e10148-16a8-432a-b86d-ef620c3e48ef",
        "isEnabled": true,
        "value": "HpcUsers"
        }
    ],
    ```
7. Mentse a fájlt. A portálon, kattintson a **kezelése Manifest** > **feltöltése Manifest**. A szerkesztett jegyzékfájl majd feltöltheti.
8. Kattintson a **felhasználók**, válasszon ki egy felhasználót, és kattintson a **hozzárendelése**. Az elérhető szerepkörök (HpcUsers vagy HpcAdminMirror) egyik hozzárendeléséhez a felhasználóhoz. Ismételje meg ezt a címtárban további felhasználókkal. Háttér-információkat fürt felhasználók, lásd: [fürt felhasználók kezelése](https://technet.microsoft.com/library/ff919335(v=ws.11).aspx).

   > [!NOTE] 
   > Felhasználók kezelése, azt javasoljuk, az Azure Active Directory preview paneljén a [Azure-portálon](https://portal.azure.com).
   >


## <a name="step-2-register-the-hpc-cluster-client-with-your-azure-ad-tenant"></a>2. lépés: A HPC-fürt ügyfél regisztrálása az Azure AD-bérlő

1. Jelentkezzen be a [klasszikus Azure portálra](https://manage.windowsazure.com).
2. Kattintson a **Active Directory** a bal oldali menüben, majd kattintson a kívánt könyvtár az előfizetésben. A címtárban található erőforrások hozzáféréssel kell rendelkeznie.
3. Kattintson a **alkalmazások** > **Hozzáadás**, és kattintson a **a szerveztem által fejlesztett alkalmazás hozzáadása**. A varázslóban adja meg a következő információkat:

    * **Név** -HPCPackClusterClient
    * **Típus** – Itt adhatja meg **natív ügyfélalkalmazás**
    * **Átirányítási URI** - `http://hpcclient`

4. Az alkalmazás hozzáadása után kattintson **konfigurálása**. Másolás a **ügyfél-azonosító** értékét, és mentse azt. Később szüksége az alkalmazás konfigurálásakor.

5. A **egyéb alkalmazások engedélyei**, kattintson a **alkalmazás hozzáadása**. Keresse meg és vegye fel az HpcPackClusterServer alkalmazást (1. lépésben létrehozott).

6. Az a **delegált engedélyek** legördülő menüből válassza **hozzáférés HpcClusterServer**. Ezután kattintson a **Save** (Mentés) gombra.


## <a name="step-3-configure-the-hpc-cluster"></a>3. lépés: A HPC-fürt konfigurálása

1. Az Azure-ban a HPC Pack 2016 átjárócsomópontjához csatlakozik.

2. Indítsa el a HPC PowerShell.

3. Futtassa az alábbi parancsot:

    ```powershell

    Set-HpcClusterRegistry -SupportAAD true -AADInstance https://login.microsoftonline.com/ -AADAppName HpcClusterServer -AADTenant <your AAD tenant name> -AADClientAppId <client ID> -AADClientAppRedirectUri http://hpcclient
    ```
    Ha

    * `AADTenant`Adja meg például az Azure AD-bérlő neve`hpclocal.onmicrosoft.com`
    * `AADClientAppId`Adja meg a 2. lépésben létrehozott alkalmazás ügyfél-azonosító.

4. Indítsa újra a HpcSchedulerStateful szolgáltatást.

    Több átjárócsomópontokkal a fürtben az átjárócsomópont Váltás az elsődleges másodpéldány a HpcSchedulerStateful szolgáltatás a következő PowerShell-parancsok futtathatja:

    ```powershell
    Connect-ServiceFabricCluster

    Move-ServiceFabricPrimaryReplica –ServiceName “fabric:/HpcApplication/SchedulerStatefulService”

    ```

## <a name="step-4-manage-and-submit-jobs-from-the-client"></a>4. lépés: A kezelése, valamint az ügyfél feladatok elküldéséhez

A HPC Pack ügyfél segédeszközök telepítése a számítógépre, töltse le a Microsoft Download Center a HPC Pack 2016-telepítőfájlok (teljes telepítés). Ha a telepítés megkezdéséhez válassza a telepítő beállítás megadása a **HPC Pack ügyfél segédprogramok**.

Az ügyfélszámítógép előkészítése során használt tanúsítványt telepítenie [HPC-fürttelepítés](hpcpack-2016-cluster.md) az ügyfélszámítógépen. Szabványos Windows tanúsítvány felügyeleti eljárások segítségével telepíthet a nyilvános tanúsítvány a **tanúsítványok – aktuális felhasználó** > **megbízható legfelső szintű hitelesítésszolgáltatók** tárolja. 

Most futtassa a HPC Pack parancsokat, vagy a HPC Pack Feladatkezelő grafikus felhasználói felület segítségével küldje el, majd a fürt feladatok kezelése az Azure AD-fiók használatával. További feladatok küldésének beállításai: [egy HPC Pack feladatok HPC küldje el a fürt az Azure-ban](hpcpack-cluster-submit-jobs.md#step-3-run-test-jobs-on-the-cluster).

> [!NOTE]
> Először az Azure-ban a HPC Pack fürt csatlakozni próbál, megjelenik egy felugró ablakokat. Adja meg az Azure AD hitelesítő adatait a bejelentkezéshez. A token gyorsítótárába. A fürthöz az Azure-ban a későbbi kapcsolatok a gyorsítótárazott token használja, ha a hitelesítés módosításai, vagy a gyorsítótárazott nincs bejelölve.
>
  
Például az előző lépések végrehajtását követően alapján is kereshet feladatokat a helyi ügyfélről az alábbiak szerint:

```powershell 
Get-HpcJob –State All –Scheduler https://<Azure load balancer DNS name> -Owner <Azure AD account>
```

## <a name="useful-cmdlets-for-job-submission-with-azure-ad-integration"></a>Az Azure AD-integrációs feladat elküldése hasznos parancsmagjai 

### <a name="manage-the-local-token-cache"></a>A helyi jogkivonat gyorsítótára kezelése

HPC Pack 2016 két új HPC PowerShell-parancsmagokat kínál a helyi jogkivonat gyorsítótár kezelésére. Ezek a parancsmagok hasznosak feladatok nem interaktív elküldése. Tekintse meg a következő példát:

```powershell
Remove-HpcTokenCache

$SecurePassword = "<password>" | ConvertTo-SecureString -AsPlainText -Force

Set-HpcTokenCache -UserName <AADUsername> -Password $SecurePassword -scheduler https://<Azure load balancer DNS name> 
```

### <a name="set-the-credentials-for-submitting-jobs-using-the-azure-ad-account"></a>Állítsa be a hitelesítő adatokat az Azure AD-fiókot használó feladatok elküldése 

Egyes esetekben érdemes lehet futtatni a feladatot a HPC-fürt felhasználói (az egy tartományhoz csatlakozó HPC-fürtben, futtassa egy tartomány mailekhez; a nem tartományhoz csatlakoztatott HPC-fürtre, az átjárócsomópont futtassa egy helyi felhasználói).

1. Állítsa be a hitelesítő adatokat használja a következő parancsokat:

    ```powershell
    $localUser = “<username>”

    $localUserPassword=”<password>”

    $secpasswd = ConvertTo-SecureString $localUserPassword -AsPlainText -Force

    $mycreds = New-Object System.Management.Automation.PSCredential ($localUser, $secpasswd)

    Set-HpcJobCredential -Credential $mycreds -Scheduler https://<Azure load balancer DNS name>
    ```

2. Majd elküldeni a feladatot az alábbiak szerint. A feladat vagy tevékenység fut a $localUser a számítási csomópontok.

    ```powershell
    $emptycreds = New-Object System.Management.Automation.PSCredential ($localUser, (new-object System.Security.SecureString))
    ...
    $job = New-HpcJob –Scheduler https://<Azure load balancer DNS name>

    Add-HpcTask -Job $job -CommandLine "ping localhost" -Scheduler https://<Azure load balancer DNS name>

    Submit-HpcJob -Job $job -Scheduler https://<Azure load balancer DNS name> -Credential $emptycreds
    ```
    
   Ha `–Credential` nincs megadva `Submit-HpcJob`, a feladat vagy tevékenység fut, az Azure AD-fiók egy helyi csatlakoztatott felhasználói. (A HPC-fürtben hoz létre egy helyi felhasználói neve megegyezik az Azure AD-fiókot a feladat futtatására.)
    
3. Határozza meg az Azure AD-fiókot kiterjesztett dátumát. Ez akkor hasznos, ha egy MPI feladat futó Linux-csomópontokat használja az Azure AD-fiókot.

   * Állítsa be az Azure AD-fiókot, maga a kibővített adatok

      ```powershell
      Set-HpcJobCredential -Scheduler https://<Azure load balancer DNS name> -ExtendedData <data> -AadUser
      ```
      
   * Állítsa be a kibővített adatok és a HPC-fürt felhasználóként
   
      ```powershell
      Set-HpcJobCredential -Credential $mycreds -Scheduler https://<Azure load balancer DNS name> -ExtendedData <data>
      ```

