---
title: "Használatával a privilegizált végpont Azure verem |} Microsoft Docs"
description: "Bemutatja, hogyan használja a rendszerjogosultságú végpontot Azure-készletben (az Azure-verem operátor)."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: e94775d5-d473-4c03-9f4e-ae2eada67c6c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: mabrigg
ms.openlocfilehash: 949715317de69064bb66fb470a805e367512bd6f
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="using-the-privileged-endpoint-in-azure-stack"></a>Használatával a privilegizált végpont Azure verem

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Azure verem kezelőként használja az adminisztrációs portálhoz, a PowerShell vagy Azure Resource Manager API-k a napi felügyeleti feladatok. Azonban az egyes közös műveletek kisebb kell használnia a *kiemelt végpont*. Ehhez a végponthoz egy előre konfigurált távoli PowerShell-konzolt, és segítséget nyújtanak a szükséges feladat elvégzésére éppen elegendő képességeket. A végpont PowerShell JEA (csak elég felügyeleti) teszi közzé a parancsmagok csak egy korlátozott készletét használja. Hozzáférni a privilegizált végpontot, és meghívja a parancsmagok korlátozott készletét, egy alacsony jogosultsági szintű fiókot használja. Nem rendszergazdai fiókok szükség. A fokozott biztonság érdekében parancsfájlok nem engedélyezett.

A privilegizált végpont használhatja például a következő feladatok elvégzéséhez:

- Alacsony szintű feladatokat végezhet, például a [diagnosztikai naplók gyűjtésére](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostics#log-collection-tool).
- Sok telepítés utáni datacenter integrációs feladatok elvégzéséhez integrált rendszerekhez, mint például a tartománynévrendszer (DNS) továbbítók hozzáadása a központi telepítést követően a Graph-integráció, az Active Directory összevonási szolgáltatások (AD FS), a tanúsítvány beállítása Elforgatás, stb.
- Használható integrált rendszer részletes hibaelhárítási ideiglenes, magas szintű hozzáférést szerezni a támogatási szolgálathoz. 

A privilegizált végpont minden művelet (és a megfelelő kimeneti), amelyet a PowerShell-munkamenetet a naplózza. Így lehetővé teszi a teljes átlátszóságnak és a műveletek teljes naplózását. Ezekben a naplófájlokban jövőbeli naplózási célokra őrizheti meg.

> [!NOTE]
> Az Azure verem Development Kit (ASDK), futtathatja a rendszerjogosultságú végpont elérhető parancsainak közvetlenül egy PowerShell-munkamenetet a development kit gazdagépen. Azonban előfordulhat, hogy vizsgálni kívánt egyes műveletek használatával a privilegizált végpont, például naplógyűjtést, mert ez az egyetlen módszer, amely elérhető egy integrált rendszerek környezetben bizonyos műveletek végrehajtásához.

## <a name="access-the-privileged-endpoint"></a>Hozzáférés a rendszerjogosultságú végpont

A privilegizált végpont a virtuális gépen, amely üzemelteti a privilegizált végpont PowerShell távoli kapcsolaton keresztül érhető el. A virtuális gép a ASDK AzS-ERCS01 neve. Egy integrált rendszert használ, ha nincsenek három alkalmazáspéldányra a rendszerjogosultságú végpont, amelyeken a virtuális gépen belüli (*előtag*-ERCS01, *előtag*-ERCS02, vagy *előtag*-ERCS03) a különböző gazdagépeken, a rugalmasságot. 

Ezt az eljárást egy integrált rendszer megkezdése előtt győződjön meg arról, hogy van-e hozzáférési jogosultsággal rendelkező végpont IP-címe vagy DNS-en keresztül. Azure verem kezdeti telepítés után a rendszerjogosultságú végpont csak való hozzáféréshez IP-cím, mert a DNS-integráció van még nem állította be. A számítógép-Gyártói hardver gyártójától tudhatja meg "AzureStackStampDeploymentInfo", amely tartalmazza a rendszerjogosultságú végpont IP-címek nevű JSON-fájlt.

Azt javasoljuk, hogy csatlakozni a rendszerjogosultságú végpont csak a hardver életciklus gazdagépről vagy egy dedikált, zárolt számítógép, például egy [Privileged Access munkaállomás](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations).

1. A környezettől függően az alábbi módszerekkel:

    - Az integrált rendszeren futtassa a következő parancs futtatásával adja hozzá a kiemelt végpont a hardver életciklus gazdagép vagy a Privileged Access munkaállomás megbízható gazdagépként.

      ````PowerShell
        winrm s winrm/config/client '@{TrustedHosts="<IP Address of Privileged Endpoint>"}'
      ````
    - Ha a ADSK fut, jelentkezzen be a fejlesztési kit állomás.

2. A hardver életciklus gazdagép vagy a Privileged Access munkaállomás nyisson meg egy rendszergazda jogú Windows PowerShell-munkamenetben. A következő parancsokat a virtuális gépen, amely üzemelteti a privilegizált végpont távoli munkamenetet hozhat létre:
 
    - Az integrált rendszeren:
      ````PowerShell
        $cred = Get-Credential

        Enter-PSSession -ComputerName <IP_address_of_ERCS>`
          -ConfigurationName PrivilegedEndpoint -Credential $cred
      ````
      A `ComputerName` az IP-cím vagy a DNS-neve, amely üzemelteti a privilegizált végpont a virtuális gépek közül az egyik paraméter lehet. 
    - Ha a ADSK fut:
     
      ````PowerShell
        $cred = Get-Credential

        Enter-PSSession -ComputerName azs-ercs01`
          -ConfigurationName PrivilegedEndpoint -Credential $cred
      ```` 
   Amikor a rendszer kéri, a következő hitelesítő adatok használata:

      - **Felhasználónév**: formátumban adja meg a CloudAdmin fiók  **&lt;* Azure verem tartomány*&gt;\cloudadmin**. (ASDK, a felhasználónév nem **azurestack\cloudadmin**.)
      - **Jelszó**: Adja meg ugyanazt a jelszót a AzureStackAdmin tartományi rendszergazdai fiók a telepítés során megadott.
    
3.  Miután csatlakozott, a kérdés módosul  **[*IP-cím vagy ERCS virtuális gép neve*]: PS > ** vagy **[azs-ercs01]: PS >**, attól függően, a környezet. Itt futtatása `Get-Command` elérhető parancsmagok listájának megtekintéséhez.

    ![Get-Command parancsmaggal kimeneti ábrázoló elérhető parancsok listája](media/azure-stack-privileged-endpoint/getcommandoutput.png)

    Ezek a parancsmagok számos szánt csak integrált rendszer környezetekben (például a datacenter-integrációhoz kapcsolódó parancsmagok). Az a ASDK érvényesítése a következő parancsmagokat:

    - Törölje az állomás
    - Zárja be-PrivilegedEndpoint
    - Kilépés-PSSession
    - Get-AzureStackLog
    - Get-AzureStackStampInformation
    - Get-Command
    - Get-FormatData
    - Get-Help
    - Get-ThirdPartyNotices
    - Mértékcsoport-objektum
    - Új CloudAdminUser
    - Kimenő alapértelmezett
    - Remove-CloudAdminUser
    - Select-Object
    - Set-CloudAdminUserPassword
    - STOP-AzureStack
    - Get-ClusterLog

4.  Parancsfájl-kezelési nem engedélyezett, mert a kiegészítést az paraméterértékeket nem használhat. Ahhoz, hogy a paraméterek listáját egy adott parancsmag, a következő parancsot:

    ````PowerShell
    Get-Command <cmdlet_name> -Syntax
    ```` 
    Ha bármilyen típusú parancsfájl műveletet, a hiba miatt sikertelen a művelet **ScriptsNotAllowed**. Ez az elvárt működés.

## <a name="close-the-privileged-endpoint-session"></a>Zárja be a kiemelt végpont munkamenet

 Amint azt korábban említettük, a kiemelt végpont naplózza minden művelet (és a megfelelő kimeneti), amelyet a PowerShell-munkamenetet a. Akkor zárja be a munkamenet használatával a `Close-PrivilegedEndpoint` parancsmag. Ez a parancsmag megfelelően bezárása után a végpont, és egy külső fájlmegosztás meg, megőrzés adja át a rendszernapló fájljaiban.

A végpont munkamenet lezárása:

1. A privilegizált végpont által elérhető külső fájlmegosztás létrehozása. Fejlesztői csomag környezetben csak a development kit gazdagépen hozhat létre fájlmegosztást.
2. Futtassa a `Close-PrivilegedEndpoint` parancsmag. 
3. Egy elérési utat, amelyen a Beszélgetés szövegének naplófájl tárolási kéri. Adja meg a fájlmegosztás létrehozott korábban, a formátum &#92; &#92; *kiszolgálónév*&#92; *megosztásnév*. Ha nem ad meg egy elérési utat, a parancsmag futtatása sikertelen, és a munkamenet nyitva marad. 

    ![Zárja be-PrivilegedEndpoint parancsmag kimenetében, amely mutatja, amelyben meg kell határoznia a Beszélgetés szövegének cél elérési útja](media/azure-stack-privileged-endpoint/closeendpoint.png)

Után a Beszélgetés szövegének naplófájlok sikeresen átkerülnek a fájlmegosztáshoz, azok még automatikusan törli a kiemelt végpont. Ha a parancsmagok segítségével zárja be a kiemelt végpont munkamenet `Exit-PSSession` vagy `Exit`, vagy csak zárja be a PowerShell-konzolt, a Beszélgetés szövegének naplók nem átvitele egy fájlmegosztást. A privilegizált végpont maradjanak. A következő futtatásakor `Close-PrivilegedEndpoint` egy fájlmegosztást, és a Beszélgetés szövegének naplók az előző munkamenet is át.

## <a name="next-steps"></a>Következő lépések
[Azure-verem diagnosztikai eszközök](azure-stack-diagnostics.md)







