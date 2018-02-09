---
title: "Használatával a privilegizált végpont Azure verem |} Microsoft Docs"
description: "Bemutatja, hogyan használja a rendszerjogosultságú végpontot (EGP) Azure-készletben (az Azure-verem operátor)."
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
ms.date: 01/25/2018
ms.author: mabrigg
ms.openlocfilehash: 34ad4d7038202bd5efa2b3c210571268a39bf278
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="using-the-privileged-endpoint-in-azure-stack"></a>Használatával a privilegizált végpont Azure verem

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Azure verem kezelőként használja az adminisztrációs portálhoz, a PowerShell vagy Azure Resource Manager API-k a napi felügyeleti feladatok. Azonban az egyes közös műveletek kisebb kell használnia a *kiemelt végpont* (EGP). Az EGP egy előre konfigurált távoli PowerShell-konzolt, és segítséget nyújtanak a szükséges feladat elvégzésére éppen elegendő képességeket. A végpont által használt [PowerShell JEA (csak elég felügyeleti)](https://docs.microsoft.com/en-us/powershell/jea/overview) teszi közzé a parancsmagok csak egy korlátozott készletét. Az EGP eléréséhez, és meghívja a parancsmagok korlátozott készletét, egy alacsony jogosultsági szintű fiókot használja. Nem rendszergazdai fiókok szükség. A fokozott biztonság érdekében parancsfájlok nem engedélyezett.

Az EGP használhatja például a következő feladatok elvégzéséhez:

- Alacsony szintű feladatokat végezhet, például a [diagnosztikai naplók gyűjtésére](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostics#log-collection-tool).
- Sok telepítés utáni datacenter integrációs feladatok elvégzéséhez integrált rendszerekhez, mint például a tartománynévrendszer (DNS) továbbítók hozzáadása a központi telepítést követően a Microsoft Graph-integráció, az Active Directory összevonási szolgáltatások (AD FS) integrációs beállítása tanúsítvány elforgatás, stb.
- Használható integrált rendszer részletes hibaelhárítási ideiglenes, magas szintű hozzáférést szerezni a támogatási szolgálathoz.

Az EGP minden művelet (és a megfelelő kimeneti), amelyet a PowerShell-munkamenetet a naplózza. Így lehetővé teszi a teljes átlátszóságnak és a műveletek teljes naplózását. Ezekben a naplófájlokban jövőbeli naplózások őrizheti meg.

> [!NOTE]
> Az az Azure verem Development Kit (ASDK), futtathatja az EGP elérhető parancsainak közvetlenül egy PowerShell-munkamenetet a development kit gazdagépen. Érdemes lehet azonban az EGP naplógyűjtést, például egyes műveletek tesztelése, mert ez az egyetlen módszer, amely elérhető egy integrált rendszerek környezetben bizonyos műveletek végrehajtásához.

## <a name="access-the-privileged-endpoint"></a>Hozzáférés a rendszerjogosultságú végpont

A távoli PowerShell-munkamenetet a virtuális gépen, amelyen az EGP keresztül érhető el az EGP. A ASDK, a virtuális gép neve **AzS-ERCS01**. Ha egy integrált rendszer használata esetén az EGP, minden futó három példánya van egy virtuális gépen (*előtag*-ERCS01, *előtag*-ERCS02, vagy *előtag*- ERCS03) a különböző gazdagépeken, a rugalmasságot. 

Ezt az eljárást egy integrált rendszer megkezdése előtt győződjön meg arról végezheti el az EGP, IP-címe vagy DNS-en keresztül. Azure-vermet, a kezdeti telepítés után hozzáférhet az EGP csak IP-cím szerint, mert a DNS-integráció van még nem állította be. OEM hardvergyártójához nevű JSON-fájlt biztosít **AzureStackStampDeploymentInfo** , amely tartalmazza a EGP IP-címeket.

Azt javasoljuk, hogy csatlakozni az EGP csak a hardver életciklus gazdagépről vagy egy dedikált, biztonságos számítógép, például egy [Privileged Access munkaállomás](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations).

1. Az emelt szintű hozzáférés munkaállomás eléréséhez.

    - Az integrált rendszeren futtassa a következő parancs futtatásával adja hozzá az EGP a hardver életciklus gazdagép vagy a Privileged Access munkaállomás megbízható gazdagépként.

      ````PowerShell
        winrm s winrm/config/client '@{TrustedHosts="<IP Address of Privileged Endpoint>"}'
      ````
    - Ha a ADSK fut, jelentkezzen be a fejlesztési kit állomás.

2. A hardver életciklus gazdagép vagy a Privileged Access munkaállomás nyisson meg egy rendszergazda jogú Windows PowerShell-munkamenetben. A következő parancsokat a virtuális gépen, amelyen az EGP távoli munkamenetet hozhat létre:
 
    - Az integrált rendszeren:
      ````PowerShell
        $cred = Get-Credential

        Enter-PSSession -ComputerName <IP_address_of_ERCS>`
          -ConfigurationName PrivilegedEndpoint -Credential $cred
      ````
      A `ComputerName` az IP-cím vagy a DNS-neve, amelyen az EGP a virtuális gépek közül az egyik paraméter lehet. 
    - Ha a ADSK fut:
     
      ````PowerShell
        $cred = Get-Credential

        Enter-PSSession -ComputerName azs-ercs01`
          -ConfigurationName PrivilegedEndpoint -Credential $cred
      ```` 
   Amikor a rendszer kéri, a következő hitelesítő adatok használata:

      - **Felhasználónév**: formátumban adja meg a CloudAdmin fiók  **&lt; *Azure verem tartomány*&gt;\cloudadmin**. (ASDK, a felhasználónév nem **azurestack\cloudadmin**.)
      - **Jelszó**: Adja meg ugyanazt a jelszót a AzureStackAdmin tartományi rendszergazdai fiók a telepítés során megadott.
    
3.  Miután csatlakozott, a kérdés módosul **[*IP-cím vagy ERCS virtuális gép neve*]: PS >** vagy **[azs-ercs01]: PS >**, attól függően, a környezet. Itt futtatása `Get-Command` elérhető parancsmagok listájának megtekintéséhez.

    Ezek a parancsmagok számos szánt csak integrált rendszer környezetekben (például a datacenter-integrációhoz kapcsolódó parancsmagok). Az a ASDK érvényesítése a következő parancsmagokat:

    - Clear-Host
    - Close-PrivilegedEndpoint
    - Kilépés-PSSession
    - Get-AzureStackLog
    - Get-AzureStackStampInformation
    - Get-Command
    - Get-FormatData
    - Get-Help
    - Get-ThirdPartyNotices
    - Mértékcsoport-objektum
    - New-CloudAdminUser
    - Kimenő alapértelmezett
    - Remove-CloudAdminUser
    - Select-Object
    - Set-CloudAdminUserPassword
    - Test-AzureStack
    - Stop-AzureStack
    - Get-ClusterLog

## <a name="tips-for-using-the-privileged-endpoint"></a>Tippek a rendszerjogosultságú végpont 

Említetteknek megfelelően van-e az EGP egy [PowerShell JEA](https://docs.microsoft.com/en-us/powershell/jea/overview) végpont. Adja meg egy erős biztonsági réteg, JEA-végpont csökkenti néhány alapvető PowerShell funkciót, például parancsfájlok vagy a lap befejezését. Ha bármilyen típusú parancsfájl műveletet, a hiba miatt sikertelen a művelet **ScriptsNotAllowed**. Ez az elvárt működés.

Igen például ahhoz, hogy a paraméterek listáját egy adott parancsmag, akkor futtassa a következő parancsot:

```PowerShell
    Get-Command <cmdlet_name> -Syntax
```

Másik lehetőségként használhatja a [Import-PSSession](https://docs.microsoft.com/en-us/powershell/module/Microsoft.PowerShell.Utility/Import-PSSession?view=powershell-5.1) parancsmag segítségével importálja a EGP-parancsmagok az aktuális munkamenetbe, a helyi számítógépen. Így minden parancsmagok és a EGP funkcióit is elérhető a helyi számítógépen, és az kiegészítést, több általában parancsfájlok. 

Importálja a EGP munkamenet a helyi gépén, tegye a következőket:

1. Az emelt szintű hozzáférés munkaállomás eléréséhez.

    - Az integrált rendszeren futtassa a következő parancs futtatásával adja hozzá az EGP a hardver életciklus gazdagép vagy a Privileged Access munkaállomás megbízható gazdagépként.

      ````PowerShell
        winrm s winrm/config/client '@{TrustedHosts="<IP Address of Privileged Endpoint>"}'
      ````
    - Ha a ADSK fut, jelentkezzen be a fejlesztési kit állomás.

2. A hardver életciklus gazdagép vagy a Privileged Access munkaállomás nyisson meg egy rendszergazda jogú Windows PowerShell-munkamenetben. A következő parancsokat a virtuális gépen, amelyen az EGP távoli munkamenetet hozhat létre:
 
    - Az integrált rendszeren:
      ````PowerShell
        $cred = Get-Credential

        $session = New-PSSession -ComputerName <IP_address_of_ERCS>`
          -ConfigurationName PrivilegedEndpoint -Credential $cred
      ````
      A `ComputerName` az IP-cím vagy a DNS-neve, amelyen az EGP a virtuális gépek közül az egyik paraméter lehet. 
    - Ha a ADSK fut:
     
      ````PowerShell
       $cred = Get-Credential

       $session = New-PSSession -ComputerName azs-ercs01`
          -ConfigurationName PrivilegedEndpoint -Credential $cred
      ```` 
   Amikor a rendszer kéri, a következő hitelesítő adatok használata:

      - **Felhasználónév**: formátumban adja meg a CloudAdmin fiók  **&lt; *Azure verem tartomány*&gt;\cloudadmin**. (ASDK, a felhasználónév nem **azurestack\cloudadmin**.)
      - **Jelszó**: Adja meg ugyanazt a jelszót a AzureStackAdmin tartományi rendszergazdai fiók a telepítés során megadott.

3. A EGP munkamenet importálja a helyi számítógép
    ````PowerShell 
        Import-PSSession $session
    ````
4. Most kiegészítést használja, és hajtsa végre a parancsfájl-kezelési szokásos módon a helyi PowerShell-munkamenetet a funkciók és a EGP, a parancsmag a meghatározott biztonsági előírások Azure verem csökkentése nélkül. Jó munkát!


## <a name="close-the-privileged-endpoint-session"></a>Zárja be a kiemelt végpont munkamenet

 Amint azt korábban említettük, az EGP naplózza minden művelet (és a megfelelő kimeneti) a PowerShell-munkamenetben végre. Akkor zárja be a munkamenet használatával a `Close-PrivilegedEndpoint` parancsmag. Ez a parancsmag megfelelően bezárása után a végpont, és egy külső fájlmegosztás meg, megőrzés adja át a rendszernapló fájljaiban.

A végpont munkamenet lezárása:

1. Az EGP által elérhető külső fájlmegosztás létrehozása. Fejlesztői csomag környezetben csak a development kit gazdagépen hozhat létre fájlmegosztást.
2. Futtassa a `Close-PrivilegedEndpoint` parancsmag. 
3. Egy elérési utat, amelyen a Beszélgetés szövegének naplófájl tárolási kéri. Adja meg a fájlmegosztás létrehozott korábban, a formátum &#92; &#92; *kiszolgálónév*&#92; *megosztásnév*. Ha nem ad meg egy elérési utat, a parancsmag futtatása sikertelen, és a munkamenet nyitva marad. 

    ![Zárja be-PrivilegedEndpoint parancsmag kimenetében, amely mutatja, amelyben meg kell határoznia a Beszélgetés szövegének cél elérési útja](media/azure-stack-privileged-endpoint/closeendpoint.png)

A Beszélgetés szövegének naplófájlok sikeresen átkerülnek a fájlmegosztáshoz, miután azok még automatikusan törli a az EGP. Ha a EGP munkamenet bezárásakor a parancsmagok használatával `Exit-PSSession` vagy `Exit`, vagy csak zárja be a PowerShell-konzolt, a Beszélgetés szövegének naplók nem átvitele egy fájlmegosztást. Az EGP maradjanak. A következő futtatásakor `Close-PrivilegedEndpoint` egy fájlmegosztást, és a Beszélgetés szövegének naplók az előző munkamenet is át.

## <a name="next-steps"></a>További lépések
[Azure-verem diagnosztikai eszközök](azure-stack-diagnostics.md)