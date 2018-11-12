---
title: Az Azure-ban fürtök egy HPC Pack-feladatok elküldése |} A Microsoft Docs
description: Ismerje meg, hogyan állítható be egy helyszíni számítógépen küldhetők be feladatok a HPC Pack-fürthöz az Azure-ban
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management,hpc-pack
ms.assetid: 78f6833c-4aa6-4b3e-be71-97201abb4721
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: big-compute
ms.date: 05/14/2018
ms.author: danlep
ms.openlocfilehash: ce8e2457c1d575e890174de3b9cf7faf6e16a7cb
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51258816"
---
# <a name="submit-hpc-jobs-from-an-on-premises-computer-to-an-hpc-pack-cluster-deployed-in-azure"></a>HPC-feladatok elküldése helyi számítógépről Azure-ban üzembe helyezett HPC Pack-fürtnek
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Ügyfél küldhetők be feladatok a helyszíni számítógép konfigurálása egy [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) fürt az Azure-ban. Ez a cikk bemutatja, hogyan állítható be a helyi számítógép ügyféleszközökhöz való elküldeni a feladatot HTTPS-kapcsolaton keresztül a fürt az Azure-ban. Ezzel a módszerrel több fürt felhasználó küldhet feladatokat egy felhőalapú HPC Pack-fürthöz, de a virtuális gép átjárócsomóponthoz való közvetlen csatlakozás lehetőségét, vagy fér hozzá az Azure-előfizetés nélkül.

![Küldjön el egy feladatot egy fürtöt az Azure-ban][jobsubmit]

## <a name="prerequisites"></a>Előfeltételek
* **Egy Azure-beli virtuális gépen telepített HPC Pack átjárócsomópont** – javasoljuk, hogy használjon automatizált eszközökkel például egy [Azure gyorsindítási sablon](https://azure.microsoft.com/documentation/templates/) a fő csomópontot és a fürt üzembe helyezéséhez. A DNS-nevét a fő csomópontot és a jelen cikkben ismertetett lépések végrehajtásához egy fürt rendszergazda hitelesítő adatait kell.
* **Ügyfélszámítógép** – HPC Pack ügyfél segédprogramok futtatható Windows vagy Windows Server ügyfél számítógépre van szüksége (lásd: [rendszerkövetelmények](https://technet.microsoft.com/library/dn535781.aspx)). Ha csak szeretné küldhetők be feladatok a HPC Pack webes portálon vagy REST API-t használja, minden olyan ügyfélszámítógépen, a választott is használhatja.
* **HPC Pack telepítési adathordozó** – a HPC Pack ügyfél segédprogramok, az ingyenes telepítési csomag telepítéséhez, a HPC Pack legújabb verziója érhető el a [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=56360). Győződjön meg arról, hogy le kell tölteni a fő csomópont virtuális gép a telepített HPC Pack ugyanazt a verzióját.

## <a name="step-1-install-and-configure-the-web-components-on-the-head-node"></a>1. lépés: Telepítse és konfigurálja a webes összetevők az átjárócsomóponthoz
Ahhoz, hogy egy REST-felület küldhetők be feladatok a a fürt HTTPS-kapcsolaton keresztül, ellenőrizze, hogy a HPC Pack webösszetevők a HPC Pack központi csomóponton. Ha ezek még nincsenek telepítve, először telepítse a webes összetevők HpcWebComponents.msi telepítőfájl futtatásával. Ezt követően konfigurálja a HPC PowerShell-parancsprogram futtatásával az összetevőket **Set-HPCWebComponents.ps1**.

Szükséges részletes eljárásokért lásd: [telepítse a Microsoft HPC Pack webösszetevők](https://technet.microsoft.com/library/hh314627.aspx).

> [!TIP]
> Bizonyos Azure-gyorssablonok a HPC Pack-fürtök telepítése, és a webes összetevők automatikus konfigurálásához.
> 
> 

**A webes összetevők telepítése**

1. Csatlakozás az átjárócsomóponthoz virtuális gép a fürt rendszergazdai hitelesítő adataival.
1. A HPC Pack telepítési mappából futtassa HpcWebComponents.msi a fő csomópontot.
1. Kövesse a varázsló a webes összetevők telepítése

**A webes összetevők konfigurálása**

1. A központi csomóponton indítsa el a HPC PowerShell rendszergazdaként.
1. A konfigurációs parancsprogram helye könyvtár módosításához írja be a következő parancsot:
   
    ```powershell
    cd $env:CCP_HOME\bin
    ```
1. Konfigurálja a REST-felület, és indítsa el a HPC-webszolgáltatás, írja be a következő parancsot:
   
    ```powershell
    .\Set-HPCWebComponents.ps1 –Service REST –enable
    ```
1. Amikor a rendszer kéri, válassza ki a tanúsítványt, válassza ki a tanúsítványt, amely megfelel a fő csomópontot nyilvános DNS-nevét. Például, ha az átjárócsomópont a klasszikus üzemi modellt használó virtuális Gépet telepít, a tanúsítvány neve néz CN =&lt;*HeadNodeDnsName*&gt;. cloudapp.net. A Resource Manager üzemi modell használatakor a tanúsítvány neve néz CN =&lt;*HeadNodeDnsName*&gt;.&lt; *régió*&gt;. cloudapp.Azure.com formát követi.
   
   > [!NOTE]
   > Válassza ki ezt a tanúsítványt később elküldésekor feladatok az átjárócsomóponthoz a helyi számítógépről. Ne válasszon, vagy adja meg egy tanúsítványt, amely megfelel a fő csomópontot az Active Directory-tartományban lévő számítógép nevét (például: CN =*MyHPCHeadNode.HpcAzure.local*).
   > 
   > 
1. A webes portálon a feladatok elküldéséhez, írja be a következő parancsot:
   
    ```powershell
    .\Set-HPCWebComponents.ps1 –Service Portal -enable
    ```
1. A szkript befejezése után állítsa le és indítsa újra a HPC-feladatok a Feladatütemező szolgáltatás a következő parancsok beírásával:
   
    ```powershell
    net stop hpcscheduler
    net start hpcscheduler
    ```

## <a name="step-2-install-the-hpc-pack-client-utilities-on-an-on-premises-computer"></a>2. lépés: A HPC Pack ügyfél segédprogramok telepítése a helyi számítógépen.
Ha szeretné a HPC Pack ügyfél segédprogramok telepítése a számítógépre, a HPC Pack-telepítőfájlok (teljes telepítés) töltse le a [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=56360). Amikor megkezdi a telepítést, a lehetőséget a telepítő a **HPC Pack ügyfél segédprogramok**.

A HPC Pack ügyféleszközök küldhetők be feladatok a fő csomópont virtuális gép a használatához is kell a tanúsítvány exportálása a fő csomópontot, és telepítse az ügyfélszámítógépen. A tanúsítványt kell lennie. CER formátumban.

**A tanúsítvány exportálása a fő csomópont**

1. A fő csomópont egy Microsoft Management Console a helyi számítógép-fiók a tanúsítványok beépülő modul hozzáadása. A beépülő modul hozzáadása lépéseiért lásd: [hozzáadása a tanúsítványok beépülő MMC Konzolhoz](https://technet.microsoft.com/library/cc754431.aspx).
1. A konzolfán bontsa ki **tanúsítványok – helyi számítógép** > **személyes**, és kattintson a **tanúsítványok**.
1. Keresse meg a tanúsítványt, a HPC Pack webösszetevők a beállított [1. lépés: Telepítse és konfigurálja a webes összetevők a fő csomópontot](#step-1-install-and-configure-the-web-components-on-the-head-node) (például: CN =&lt;*HeadNodeDnsName* &gt;. cloudapp.net).
1. Kattintson a jobb gombbal a tanúsítványt, és kattintson a **feladatok** > **exportálása**.
1. A Tanúsítványexportáló varázslóban kattintson **tovább**, és ellenőrizze, hogy **nem, nem akarom exportálni a titkos kulcs** van kiválasztva.
1. Kövesse a hátralévő lépéseket a varázsló exportálja a tanúsítványt a DER kódolású bináris X.509 (. CER) formátumban.

**Importálja a tanúsítványt az ügyfélszámítógépen**

1. Másolja az exportált tanúsítványt a fő csomópont egy mappába, az ügyfélszámítógépen.
1. Az ügyfélszámítógépen futtassa certmgr.msc.
1. A tanúsítvány-kezelőben bontsa ki a **tanúsítványok – aktuális felhasználó** > **megbízható legfelső szintű hitelesítésszolgáltatók**, kattintson a jobb gombbal **tanúsítványok**, majd Kattintson a **feladatok** > **importálás**.
1. A tanúsítvány importálása varázslóban kattintson **tovább** és lépésekkel importálhatja a tanúsítványt a megbízható legfelső szintű hitelesítésszolgáltatók tárolójába, az átjárócsomópont exportált.

> [!TIP]
> Előfordulhat, hogy megjelenik egy biztonsági figyelmeztetés, mert a hitelesítésszolgáltatót a fő csomópontja nem ismeri fel az ügyfélszámítógép. Tesztelési célokra, figyelmen kívül hagyhatja ezt a figyelmeztetést, és fejezze be a tanúsítvány importálása.
> 
> 

## <a name="step-3-run-test-jobs-on-the-cluster"></a>3. lépés: Futtassa tesztelési feladatokat a fürtön
A konfiguráció ellenőrzéséhez próbálja meg a fürtön az Azure-ban, a helyi számítógépen futó feladatok. Például használhatja a HPC Pack grafikus eszközök vagy a parancssori parancsok küldhetők be feladatok a fürthöz. Egy olyan webes portál segítségével feladatok elküldéséhez.

**Feladat küldése parancsok futtatásához az ügyfélszámítógépen**

1. Egy ügyfélszámítógépen, amelyen telepítve vannak-e az a HPC Pack ügyfél segédprogramok indítson el egy parancssort.
1. Írjon be egy mintául szolgáló parancs. Írja be például a fürt összes feladat megtekintéséhez a következők egyikét, attól függően, a fő csomópont teljes DNS-nevét hasonló parancsot:
   
    ```command
    job list /scheduler:https://<HeadNodeDnsName>.cloudapp.net /all
    ```
   
    vagy
   
    ```command
    job list /scheduler:https://<HeadNodeDnsName>.<region>.cloudapp.azure.com /all
    ```
   
   > [!TIP]
   > A scheduler URL-CÍMÉT az átjárócsomóponthoz, nem az IP-cím teljes DNS-nevét használja. Ha az IP-címet ad meg, hibaüzenet jelenik meg hasonló "a tanúsítvány szükséges, vagy hogy egy érvényes megbízhatósági láncában, vagy a megbízható legfelső szintű tárolójában kell helyezni."
   > 
   > 
1. Amikor a rendszer kéri, írja be a felhasználónevet (a képernyő &lt;DomainName&gt;\\&lt;felhasználónév&gt;) és a HPC-fürt rendszergazdája vagy egy másik fürt felhasználójának beállított jelszót. Kiválaszthatja, hogy több feladat műveletet a helyi hitelesítő adatok tárolására.
   
    Feladatok listája jelenik meg.

**A HPC Job Manager eszköz használatához az ügyfélszámítógépen**

1. Ha korábban egy fürt felhasználó tartományi hitelesítő adatok nem tárolja, küldjön el egy feladatot, a hitelesítőadat-kezelőben a hitelesítő adatokat adhat hozzá.
   
    a. Az ügyfélszámítógép Vezérlőpultján a hitelesítőadat-kezelő elindításához.
   
    b. Kattintson a **Windows hitelesítő adatok** > **általános hitelesítő adatainak hozzáadása**.
   
    c. Adja meg az Internet-címet (például a https://&lt;HeadNodeDnsName&gt;.cloudapp.net/HpcScheduler vagy a https://&lt;HeadNodeDnsName&gt;.&lt; régió&gt;.cloudapp.azure.com/HpcScheduler), és a felhasználó nevét (&lt;DomainName&gt;\\&lt;felhasználónév&gt;) és a fürt rendszergazdája vagy egy másik jelszót fürt felhasználó konfigurált.
1. Az ügyfélszámítógépen indítsa el a HPC Job Manager használatát.
1. Az a **válassza ki a fő csomópont** párbeszédpanelen írja be az URL-címet a fő csomópontot az Azure-ban (például a https://&lt;HeadNodeDnsName&gt;. cloudapp.net vagy https://&lt;HeadNodeDnsName&gt;.&lt; régió&gt;. cloudapp.Azure.com formát követi).
   
    A HPC Job Manager megnyílik, és a fő csomópont feladatok listáját jeleníti meg.

**A központi csomóponton futó webes portál használata**

1. Indítson el egy webböngészőt az ügyfélszámítógépen, és adjon meg egy másikat, a következő címeket, attól függően, a fő csomópont teljes DNS-nevét:
   
    ```
    https://<HeadNodeDnsName>.cloudapp.net/HpcPortal
    ```
   
    vagy
   
    ```
    https://<HeadNodeDnsName>.<region>.cloudapp.azure.com/HpcPortal
    ```
1. A rendszerbiztonság párbeszédpanel, amely akkor jelenik meg írja be a HPC-fürt rendszergazdai tartományi hitelesítő adatait. (Is hozzáadhat mások fürt különböző szerepkörök. Lásd: [fürt felhasználók kezelése](https://technet.microsoft.com/library/ff919335.aspx).)
   
    A webes portálon a feladatok listanézete nyílik meg.
1. Elküldött egy mintául szolgáló feladatot, amely visszaadja a "Hello World" karakterlánc a fürtről, kattintson a **új feladat** a bal oldali navigációs menüben.
1. Az a **új feladat** lap **küldésének lap**, kattintson a **HelloWorld**. A feladat beküldése lap jelenik meg.
1. Kattintson a **elküldése**. Ha a rendszer kéri, adja meg a HPC-fürt rendszergazdai tartományi hitelesítő adatait. A feladat elküldésekor, és megjelenik a Feladatazonosító a **saját feladatok** lapot.
1. Az elküldött feladat eredményeinek megtekintéséhez kattintson a feladat azonosítója, majd **nézet feladatai** parancs eredményének megtekintéséhez (alatt **kimeneti**).

## <a name="next-steps"></a>További lépések
* Az Azure-fürtön a feladatok is küldhet a [HPC Pack REST API](https://social.technet.microsoft.com/wiki/contents/articles/7737.creating-and-submitting-jobs-by-using-the-rest-api-in-microsoft-hpc-pack-windows-hpc-server.aspx).
* Ha szeretne egy Linux-ügyfél a fürt feladatok elküldéséhez, tekintse meg a Python-mintához a [HPC Pack 2012 R2 SDK-t és a mintakódot](https://www.microsoft.com/download/details.aspx?id=41633).

<!--Image references-->
[jobsubmit]: ./media/virtual-machines-windows-hpcpack-cluster-submit-jobs/jobsubmit.png
