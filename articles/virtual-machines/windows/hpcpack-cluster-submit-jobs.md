---
title: Küldje el a fürt egy HPC Pack feladatok az Azure-ban |} Microsoft Docs
description: Megtudhatja, hogyan hozzon létre egy helyi számítógép küldje el a feladatok HPC Pack fürthöz az Azure-ban
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
ms.openlocfilehash: 025ff3dea365ab75af55f107da1fb7331861eb06
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2018
ms.locfileid: "34166369"
---
# <a name="submit-hpc-jobs-from-an-on-premises-computer-to-an-hpc-pack-cluster-deployed-in-azure"></a>HPC-feladatok elküldése helyi számítógépről Azure-ban üzembe helyezett HPC Pack-fürtnek
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Egy helyszíni ügyfélszámítógép feladatok elküldéséhez konfigurálása egy [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) fürt az Azure-ban. Ez a cikk bemutatja, hogyan állíthatja be a helyi számítógép ügyfél eszközökkel HTTPS-KAPCSOLATON keresztül elküldeni a feladatot a fürt az Azure-ban. Ezzel a módszerrel több fürt felhasználók elküldheti a feladatok HPC Pack felhőalapú fürtre, de az átjárócsomóponthoz VM való közvetlen csatlakozás vagy Azure-előfizetéssel való hozzáférés nélkül.

![Az Azure-ban fürtre feladat elküldése][jobsubmit]

## <a name="prerequisites"></a>Előfeltételek
* **Egy Azure virtuális Gépen telepített HPC Pack átjárócsomópont** -azt javasoljuk, hogy az automatikus eszközeit használja, mint egy [Azure gyors üzembe helyezés sablon](https://azure.microsoft.com/documentation/templates/) az átjárócsomópont és a fürt telepítéséhez. Az átjárócsomópont DNS-nevét és a végrehajtásához a cikkben egy fürt rendszergazdájának hitelesítő adataira van szüksége.
* **Ügyfélszámítógép** -HPC Pack ügyfél segédprogramok futtatható Windows vagy Windows Server ügyfél számítógépre van szüksége (lásd: [rendszerkövetelmények](https://technet.microsoft.com/library/dn535781.aspx)). Ha szeretné feladatok küldéséhez a HPC Pack webes portál vagy a REST API-t használja, minden ügyfélszámítógép az Ön által választott is használhatja.
* **HPC Pack telepítési adathordozó** - HPC Pack legújabb verziója érhető el a HPC Pack ügyfél segédprogramok, a szabad telepítési csomag telepítéséhez a [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=56360). Győződjön meg arról, hogy töltse le a virtuális gép átjárócsomópont telepített HPC Pack ugyanazt a verzióját.

## <a name="step-1-install-and-configure-the-web-components-on-the-head-node"></a>1. lépés: Telepítse és konfigurálja a webes összetevők az átjárócsomópont
Ahhoz, hogy a fürthöz feladatok küldéséhez a HTTPS-KAPCSOLATON keresztül egy REST-felület, ellenőrizze, hogy a HPC Pack webösszetevők a HPC Pack központi csomóponton. Ha még nincsenek telepítve, először telepítse a webes összetevők a HpcWebComponents.msi telepítési fájl futtatásával. Ezt követően konfigurálja a HPC PowerShell-parancsfájl futtatásával az összetevők **Set-HPCWebComponents.ps1**.

Szükséges részletes eljárásokért lásd: [telepítse a Microsoft HPC Pack webösszetevők](http://technet.microsoft.com/library/hh314627.aspx).

> [!TIP]
> Bizonyos Azure gyors üzembe helyezési sablonokat HPC Pack fürtök telepítse, és a webes összetevők automatikusan konfigurálja.
> 
> 

**A webes összetevők telepítéséhez**

1. Csatlakozás az átjárócsomóponthoz VM a fürt rendszergazdai fiók hitelesítő adataival.
2. A HPC Pack telepítési mappájából az átjárócsomópont HpcWebComponents.msi futtatnak.
3. Kövesse a varázsló a webes összetevők telepítéséhez

**A webes összetevők konfigurálása**

1. A head csomóponton indítsa el a HPC PowerShell rendszergazdaként.
2. Directory módosítsa a konfigurációs parancsfájl helyét, írja be a következő parancsot:
   
    ```powershell
    cd $env:CCP_HOME\bin
    ```
3. Konfigurálja a REST-felületen, és indítsa el a HPC-webszolgáltatás, írja be a következő parancsot:
   
    ```powershell
    .\Set-HPCWebComponents.ps1 –Service REST –enable
    ```
4. Válasszon ki egy tanúsítványt kéri, válassza a tanúsítványt, amely megfelel az átjárócsomópont nyilvános DNS-nevét. Például, ha az átjárócsomópont a klasszikus üzembe helyezési modellt használó virtuális gépek, a tanúsítvány neve a következőképpen néz CN =&lt;*HeadNodeDnsName*&gt;. cloudapp.net. A Resource Manager üzembe helyezési modellel használatakor a tanúsítvány neve néz CN =&lt;*HeadNodeDnsName*&gt;.&lt; *régió*&gt;. cloudapp.azure.com.
   
   > [!NOTE]
   > Választja ezt a tanúsítványt későbbi elküldésekor feladatok az átjárócsomóponthoz a helyi számítógépről. Ne válasszon, vagy adja meg egy tanúsítványt, amely megfelel-e a számítógép nevét, az átjárócsomópont az Active Directory-tartomány (például: CN =*MyHPCHeadNode.HpcAzure.local*).
   > 
   > 
5. A webes portálhoz a feladat elküldése konfigurálásához írja be a következő parancsot:
   
    ```powershell
    .\Set-HPCWebComponents.ps1 –Service Portal -enable
    ```
6. A parancsfájl befejezése után állítsa le és indítsa újra a HPC Job Feladatütemező szolgáltatás a következő parancsok beírásával:
   
    ```powershell
    net stop hpcscheduler
    net start hpcscheduler
    ```

## <a name="step-2-install-the-hpc-pack-client-utilities-on-an-on-premises-computer"></a>2. lépés: Telepítse a HPC Pack ügyfél segédeszközöket a helyi számítógépen
Ha szeretné a HPC Pack ügyfél segédprogramokat telepíthet a számítógépre, le a HPC Pack telepítési fájlok (teljes telepítés) a [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=56360). Ha a telepítés megkezdéséhez válassza a telepítő beállítás megadása a **HPC Pack ügyfél segédprogramok**.

Ha szeretné használni a HPC Pack ügyféleszközök elől az átjárócsomóponthoz VM feladatok küldéséhez, is szüksége exportálja a tanúsítványt az átjárócsomóponthoz, és telepíti az ügyfélszámítógépen. A tanúsítvány kell lennie. CER formátumú.

**A tanúsítvány exportálása a átjárócsomópont**

1. Az átjárócsomópont adja hozzá a tanúsítványok beépülő MMC konzolt a helyi számítógépfiók számára. A beépülő modul hozzáadása lépéseiért lásd: [hozzáadása a tanúsítványok beépülő MMC Konzolhoz](https://technet.microsoft.com/library/cc754431.aspx).
2. A konzolfán bontsa ki a **tanúsítványok – helyi számítógép** > **személyes**, és kattintson a **tanúsítványok**.
3. Keresse meg a tanúsítványt a HPC Pack webösszetevők a beállított [1. lépés: Telepítse és konfigurálja a webes összetevők az átjárócsomópont](#step-1:-install-and-configure-the-web-components-on-the-head-node) (például CN =&lt;*HeadNodeDnsName*&gt;. cloudapp.net).
4. Kattintson a jobb gombbal a tanúsítványt, és kattintson a **feladataival** > **exportálása**.
5. A Tanúsítványexportáló varázslóban kattintson **következő**, és győződjön meg arról, hogy **nem, nem akarom exportálni a titkos kulcs** van kiválasztva.
6. A további lépéseket a varázsló exportálja a tanúsítványt a DER kódolású bináris X.509 (. CER) formátumban.

**Az ügyfélszámítógépen a tanúsítvány importálása**

1. Másolja a tanúsítványt, az ügyfélszámítógépen egy mappába a átjárócsomópont exportált.
2. Az ügyfélszámítógépen futtassa certmgr.msc.
3. A tanúsítvány-kezelőben bontsa ki a **tanúsítványok – aktuális felhasználó** > **megbízható legfelső szintű hitelesítésszolgáltatók**, kattintson a jobb gombbal **tanúsítványok**, és kattintson a **feladataival** > **importálási**.
4. A tanúsítvány importálása varázslóban kattintson **következő** és a lépések segítségével importálja a tanúsítványt a megbízható legfelső szintű hitelesítésszolgáltatók tárolójába az átjárócsomópont exportált.

> [!TIP]
> Láthatja a biztonsági figyelmeztetést, mert a hitelesítésszolgáltató az átjárócsomópont nem ismeri fel az ügyfélszámítógépen. Tesztelési célokra, figyelmen kívül ezt a figyelmeztetést, és végezze el a tanúsítvány importálása.
> 
> 

## <a name="step-3-run-test-jobs-on-the-cluster"></a>3. lépés: Futtatás Tesztfeladatok a fürtön
Ellenőrizze a konfigurációt, próbálja meg az Azure-ban a fürtön, a helyi számítógépen futó feladatok. HPC Pack grafikus eszközöket vagy a parancssori parancsok például használható a fürt feladatok elküldéséhez. Egy webes portál segítségével feladatok elküldéséhez.

**Feladat küldése parancsok futtatásához az ügyfélszámítógépen**

1. Egy ügyfélszámítógépen, amelyen a HPC Pack ügyfél segédprogramok telepítve vannak egy parancssor elindításához.
2. Írja be egy minta parancsot. A fürt összes feladat listáját, írja be például a következők egyikére, attól függően, hogy a teljes DNS-nevét az átjárócsomópont hasonló parancs:
   
    ```command
    job list /scheduler:https://<HeadNodeDnsName>.cloudapp.net /all
    ```
   
    vagy
   
    ```command
    job list /scheduler:https://<HeadNodeDnsName>.<region>.cloudapp.azure.com /all
    ```
   
   > [!TIP]
   > A teljes DNS-nevét az átjárócsomóponthoz, ne az IP-címet használt ütemező URL. Ha az IP-címet ad meg, egy hibaüzenet jelenik meg, hasonló "a tanúsítvány szükséges, vagy egy érvényes megbízhatósági láncot vagy megbízható főtanúsítványainak tárolójába kerülnek."
   > 
   > 
3. Amikor a rendszer kéri, írja be a felhasználónevet (formájában &lt;tartománynév&gt;\\&lt;felhasználónév&gt;) és jelszavát, amely a HPC-fürt rendszergazdája vagy egy másik fürthöz felhasználó konfigurált. Választhat, helyileg több feladat műveletet a hitelesítő adatok tárolására.
   
    Feladatok listája jelenik meg.

**A HPC Job Manager eszköz használatához az ügyfélszámítógépen**

1. Ha korábban egy fürt felhasználó tartományi hitelesítő adatok nem tárolja, amikor elküld egy feladatot, a hitelesítőadat-kezelőben a hitelesítő adatokat adhat hozzá.
   
    a. Az ügyfélszámítógép Vezérlőpultján, a hitelesítőadat-kezelő indítása.
   
    b. Kattintson a **Windows hitelesítő adatok** > **egy általános hitelesítő adatok hozzáadása**.
   
    c. Adja meg az Internet címet (például https://&lt;HeadNodeDnsName&gt;.cloudapp.net/HpcScheduler vagy a https://&lt;HeadNodeDnsName&gt;.&lt; a régióban&gt;.cloudapp.azure.com/HpcScheduler), és a felhasználó nevét (&lt;tartománynév&gt;\\&lt;felhasználónév&gt;) és jelszavát, amely a fürt rendszergazdája vagy egy másik fürthöz felhasználó konfigurált.
2. Az ügyfélszámítógépen indítsa el a HPC Job Manager használatát.
3. Az a **Head csomópont kiválasztása** párbeszédpanelen írja be az átjárócsomóponthoz URL-CÍMÉT az Azure-ban (például https://&lt;HeadNodeDnsName&gt;. cloudapp.net vagy a https://&lt;HeadNodeDnsName&gt;.&lt; régió&gt;. cloudapp.azure.com).
   
    HPC Job Manager megnyílik, és a feladatok listáját jeleníti meg az átjárócsomópont.

**A head csomóponton futó webes portál használatához**

1. Indítsa el a webböngésző az ügyfélszámítógépen, és adjon meg egy, a következő címeket, attól függően, hogy a teljes DNS-nevét az átjárócsomóponthoz:
   
    ```
    https://<HeadNodeDnsName>.cloudapp.net/HpcPortal
    ```
   
    vagy
   
    ```
    https://<HeadNodeDnsName>.<region>.cloudapp.azure.com/HpcPortal
    ```
2. A biztonsági megjelenő párbeszédpanelen írja be a HPC-fürt rendszergazdája tartományi hitelesítő adatait. (Is hozzáadhat más fürt felhasználók különböző szerepkörök. Lásd: [fürt felhasználók kezelése](https://technet.microsoft.com/library/ff919335.aspx).)
   
    A webes portál nyitja meg a feladat listanézet.
3. A "Hello, World" karakterlánc a fürtből visszaadó minta feladat elküldése, kattintson a **új feladat** a bal oldali navigációs sáv.
4. Az a **új feladat** lap **küldésének lapjáról**, kattintson a **HelloWorld**. A feladat elküldése lap jelenik meg.
5. Kattintson a **nyújt**. Ha a rendszer kéri, adja meg a HPC-fürt rendszergazdája tartományi hitelesítő adatait. A feladat küldése, és a Feladatazonosítót jelenik meg a **saját feladatok** lap.
6. A beküldött feladat eredményeinek megtekintéséhez kattintson a feladat azonosítója, majd **nézet feladatai** parancs eredményének megtekintéséhez (alatt **kimeneti**).

## <a name="next-steps"></a>További lépések
* Feladatok az Azure fürtre is elküldheti a [HPC Pack REST API](http://social.technet.microsoft.com/wiki/contents/articles/7737.creating-and-submitting-jobs-by-using-the-rest-api-in-microsoft-hpc-pack-windows-hpc-server.aspx).
* Ha azt szeretné, a Linux-ügyfél a fürt feladatok küldéséhez, tekintse meg a Python minta a [HPC Pack 2012 R2 SDK és mintakód](https://www.microsoft.com/download/details.aspx?id=41633).

<!--Image references-->
[jobsubmit]: ./media/virtual-machines-windows-hpcpack-cluster-submit-jobs/jobsubmit.png
