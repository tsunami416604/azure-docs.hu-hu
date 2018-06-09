---
title: Az Azure verem telemetriai |} Microsoft Docs
description: PowerShell-lel Azure verem telemetriai beállítások beállításának módját ismerteti.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/07/2018
ms.author: jeffgilb
ms.reviewer: comartin
ms.openlocfilehash: ed3f09f942bdaa803ae8024d5c02230173190107
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/08/2018
ms.locfileid: "35248197"
---
# <a name="azure-stack-telemetry"></a>Az Azure verem telemetriai adat

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Az Azure verem telemetriai automatikusan feltölt rendszeradatok Microsoft keresztül a kapcsolódó felhasználói élményt. Microsoft csapatai Azure verem telemetriai adatokat gyűjt a felhasználói élmény javítása érdekében adatok felhasználásával. Ezek az adatok is használatos biztonsági, az állapot, a minőségi és a Teljesítményelemzés.

Egy Azure verem kezelő telemetriai vállalati központi telepítéshez értékes információkat biztosíthat, és lehetővé teszi az hangot, amelyek segítségével Azure verem alakzat jövőbeli verzióiban.

> [!NOTE]
> Beállíthatja úgy is továbbítják a használati adatokat az Azure-bA a számlázáshoz Azure verem. Ez elengedhetetlen ahhoz fizetési,-akkor-használható számlázási többcsomópontos Azure verem felhasználók. Használati jelentésben egymástól függetlenül vezérlik a telemetriai adatokból, és nincs szükség, válassza ki a kapacitás modelljét többcsomópontos felhasználókat vagy Azure verem szoftverfejlesztői készlet felhasználókhoz. Ezek a forgatókönyvek a használati jelentésben kikapcsolható [a regisztrációs parancsfájlt](https://docs.microsoft.com/azure/azure-stack/azure-stack-usage-reporting).

Azure verem telemetriai alapul, a Windows Server 2016 kapcsolódó felhasználói felület- és Telemetriabevitelt összetevő, amely használja a [esemény Windows (nyomkövetés)](https://msdn.microsoft.com/library/dn904632(v=vs.85).aspx) TraceLogging technológia összegyűjtésére és események és az adatok tárolására. Azure verem összetevők események és nyilvános operációs rendszer eseménynaplózás és a nyomkövetés API-k segítségével gyűjtött adatok közzététele ugyanazt a technológiát használja. Azure verem összetevők közé tartoznak a szolgáltatók: hálózati erőforrás, a tárolási erőforrás, a figyelés erőforrás és a frissítés erőforrás. A kapcsolódó felhasználói élményt és Telemetriai összetevő adatok SSL használatával titkosítja, és használja a tanúsítvány rögzítését küldött adatok HTTPS-KAPCSOLATON keresztül a Microsoft Data Management szolgáltatáshoz.

> [!IMPORTANT]
> Ahhoz, hogy a telemetriai adatok folyamata, port 443-as (HTTPS) kell megnyitni a hálózaton. A kapcsolódó felhasználói élményt és Telemetriai összetevő kapcsolódik a Microsoft adatkezelés szolgáltatás https://v10.vortex-win.data.microsoft.com. A kapcsolódó felhasználói élményt és Telemetriai összetevő is csatlakozik https://settings-win.data.microsoft.com konfigurációs információk letöltése.

## <a name="privacy-considerations"></a>Adatvédelmi megfontolások

Az ETW-szolgáltatás telemetriai adatokat továbbítja védett felhőbeli tárhelyén. A legalacsonyabb jogosultsági szint elvét végig is vezeti hozzáférés telemetriai adatokat. Csak érvényes üzleti van szükség a Microsofton hozzáférő a telemetriai adatokat. Microsoft ne használjon közös kivéve személyes adatok harmadik felekkel, az ügyfél belátása vagy leírt korlátozott céljából a [Microsoft adatvédelmi nyilatkozatát](https://privacy.microsoft.com/PrivacyStatement). OEM és partnerekkel oszt meg üzleti jelentések összesített, anonimizált adatok közé tartoznak. Egy belső Microsoft team, beleértve az adatokat, adatvédelmi és jogi felügyeleti érdekelt felek adatmegosztás döntések hozhatók.

A Microsoft tartja a, és információt minimalizálásáról eljárásokat. Igyekszünk összegyűjteni csak az, hogy szükség van, és tárolja a csak addig, amíg szükség ahhoz, hogy egy szolgáltatás vagy elemzés céljából. Hogyan működik az Azure verem rendszer és az Azure-szolgáltatásokkal kapcsolatos információk nagy részét a rendszer törli hat hónapban. Foglalja össze, vagy hosszabb ideig folyamatosan összesített adatokat.

Tudjuk, hogy az adatvédelmi és a felhasználói adatok biztonsága fontos.  A Microsoft a gondos és átfogó megközelítés ügyfelek adatainak és a felhasználói adatok Azure verem védelméről vesz igénybe. A rendszergazdák vezérlők szolgáltatásait és adatvédelmi beállítások testreszabásához bármikor rendelkezik. A kötelezettségvállalás átláthatóság és a megbízhatóság az törlése:

- A rendszer nyissa meg az ügyfelekkel kapcsolatos adatokat gyűjt a Microsoft típusú.
- Vállalati ügyfelek azt be vezérlő – azok a saját adatvédelmi beállítások is testre szabhatók.
- A Microsoft kerülnek ügyfél adatvédelmi és biztonsági előre.
- A rendszer átlátszó módjáról telemetriai adatokat lekérdezi.
- Telemetriai adatok felhasználói élmény javítására használjuk.

A Microsoft nem kíván a bizalmas adatokat, mint a hitelkártyaszámok, felhasználónevek és jelszavak, e-mail címek vagy hasonló bizalmas adatokat gyűjt. Ha azt állapítja meg, hogy bizalmas információk véletlenül érkezett, hogy törölje a parancsikont.

## <a name="examples-of-how-microsoft-uses-the-telemetry-data"></a>Példák a telemetriai adatok Microsoft általi használatára

Telemetria segít gyorsan azonosíthatja, és javítsa ki a felhasználói telepítés és konfigurációk kritikus megbízhatósági problémák fontos szerepet játszik. Információkat kaphat a telemetriai adatok segíthet a problémák azonosításához szolgáltatásokat vagy hardverkonfigurációk. A Microsoft képes ügyfelek és a meghajtó fejlesztései ezeket az adatokat a ökoszisztéma eléréséhez az integrált Azure verem megoldások minőségének sáv riasztást.

Telemetria teszi a Microsoft jobban megérteni, hogyan telepítsék központilag az ügyfél összetevők, használatát és az üzleti célok eléréséhez használhatják a szolgáltatásokat. Ezek insights segítségével rangsorolhatja mérnöki beruházások ügyfélélményt és munkaterhelések közvetlen hatással lehet területeken.

Például az ügyfél tárolók, tárolási és hálózati konfigurációk társított Azure verem szerepkörök használatát. A meghajtó és az Azure-verem kezelési és figyelési megoldások eszközintelligencia elemzéseket is használjuk. Ezek a fejlesztések könnyebben az ügyfelek számára a problémák diagnosztizálásához, és mentse azáltal, hogy kevesebb támogatási pénz meghívja a Microsoft.

## <a name="manage-telemetry-collection"></a>Telemetria gyűjtemény kezelése

Nem ajánlott Alkalmazáshasználati adatok gyűjtésének kikapcsolása a szervezetében. Azonban bizonyos esetekben erre akkor lehet szükség.

Ezekben az esetekben a rendszerleíró beállításokkal Azure verem központi telepítése előtt, vagy a telemetriai adatok végpontok Azure verem telepítése után a Microsoftnak küldött telemetriai szint konfigurálhatja.

### <a name="telemetry-levels-and-data-collection"></a>Telemetria szintek és az adatgyűjtésről

Telemetriai beállítások módosítása előtt tisztában kell lennie a telemetriai adatok szinteket, és egyes szintjein összegyűjtött adatok.

A telemetriai beállítások négy szint (0 – 3), amelyek halmozott és kategorizált a következők szerint vannak csoportosítva:

**0 (biztonság)**</br>
Csak biztonsági adatokat. Az operációs rendszer biztonsága érdekében szükséges adatokat. Ez magában foglalja a kapcsolódó felhasználói élményt és Telemetriai összetevő beállításait, és a Windows Defender adatait. Nem adott Azure verem telemetriai kibocsátott ezen a szinten.

**1 (alapszintű)**</br>
A biztonsági adatokat, és alapvető állapot és minőség adatok. Alapvető eszköz-információkat, beleértve: minőségi kapcsolatos adatokat, az alkalmazás kompatibilitási, Alkalmazáshasználati adatokat, és adatainak áttelepítését a **biztonsági** szintjét. A telemetriai adatok szint alapvető lehetővé teszi, hogy Azure verem telemetriai adatokat. Ezen a szinten gyűjtött adatokat tartalmazza:

- *Alapvető eszköz adatai* típusok és az ökoszisztéma-példánya natív és virtuális Windows Server 2016 konfigurációkkal kapcsolatos megértéséhez biztosít. Az érintett műveletek közé tartoznak az alábbiak:

  - Gép attribútumait, például az OEM és a modell.
  - Hálózatkezelés attribútumait, például a hálózati adapterek száma és a sebességét.
  - Processzor és memória attribútumait, például a magok száma és a telepített memória mennyisége.
  - Tárolási attribútumok, hány meghajtót, a meghajtó, és a meghajtó mérete típus.

- *Telemetria funkció*, többek között a feltöltött események, az eldobott események és az utolsó adatok százaléka feltöltése idő.
- *Minőségi kapcsolatos információ* , amely segít a Microsoft fejlesztése alapszinten megértse, hogyan Azure verem hajt végre. Ha például hardverkonfigurációhoz a kritikus riasztások száma.
- *Kompatibilitási adatok* , hogy segít biztosítani, arról, hogy mely erőforrás-szolgáltató van telepítve, a rendszer és a virtuális gép megértéséhez. Ez azonosítja az esetleges kompatibilitási problémákat.

**2 (speciális)**</br>
További betekintést nyerjen, többek között: az operációs rendszer és a verem Azure szolgáltatások használatáról, hogyan hajtsa végre ezeket a szolgáltatásokat, speciális megbízhatóság adatokat, és adatainak áttelepítését a **biztonsági** és **alapvető** szintek.

> [!NOTE]
> Ez az alapértelmezett telemetriai beállítása.

**3 (teljes)**</br>
Minden adatok azonosítására és a problémák elhárításának támogatása szükséges, illetve a a **biztonsági**, **alapvető**, és **bővített** szintek.

> [!IMPORTANT]
> A telemetriai adatok szinteket csak a Microsoft Azure verem összetevők vonatkoznak. A felhőszolgáltatások kívül telemetriai szinten kommunikálhatnak, nem a Microsofttól származó szoftverek összetevők és az Azure verem hardverszállító partnerétől a hardver életciklus állomáson futó szolgáltatások. Az Azure-verem megoldás hardverszolgáltatót telemetriai szabályzatát, és hogyan részt vevő, vagy nem vesznek részt együtt kell működnie.

Windows és az Azure-verem telemetria kikapcsolása is letiltja az SQL telemetriai adatokat. A Windows Server telemetriai beállítások következményeit kapcsolatos további információkért tekintse meg a [Windows Telemetriai tanulmány](https://aka.ms/winservtelemetry).

### <a name="asdk-set-the-telemetry-level-in-the-windows-registry"></a>ASDK: a telemetriai adatok szint beállítása a Windows beállításjegyzékben

A Windows Beállításszerkesztőt használatával manuális módon állítsa be a telemetriai adatok szintjét a fizikai állomáson Azure verem központi telepítése előtt. Ha a kezelési házirendek már létezik, például a csoportházirend felülírja a beállításjegyzék-beállítást.

Azure verem a development kit gazdagépen való telepítése előtt indítsa el a CloudBuilder.vhdx, és egy emelt szintű PowerShell-ablakban futtassa az alábbi parancsfájlt:

```powershell
### Get current AllowTelmetry value on DVM Host
(Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name AllowTelemetry).AllowTelemetry
### Set & Get updated AllowTelemetry value for ASDK-Host
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name "AllowTelemetry" -Value '0' # Set this value to 0,1,2,or3.  
(Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name AllowTelemetry).AllowTelemetry
```

### <a name="asdk-and-multi-node-enable-or-disable-telemetry-after-deployment"></a>ASDK és több csomópontos: engedélyezheti vagy tilthatja le a telemetriai adatokat a központi telepítést követően

Letiltása és engedélyezése telemetriai telepítés után, akkor hozzáféréssel kell rendelkeznie a a Rendszerjogosultságú végpont (EGP) amely a virtuális gépeken ERCS hagyja.

1. Engedélyezése: `Set-Telemetry -Enable`
2. Letiltása: `Set-Telemetry -Disable`

A paraméter részletei:
> . A paraméter engedélyezése – bekapcsolásához válassza a telemetriai adatok feltöltése</br>
> . PARAMÉTER Disable - kikapcsolás telemetriai adatok feltöltése  

**Parancsfájl telemetriai engedélyezése:**

```powershell
$ip = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.
$pwd= ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $pwd)
$psSession = New-PSSession -ComputerName $ip -ConfigurationName PrivilegedEndpoint -Credential $cred
Invoke-Command -Session $psSession {Set-Telemetry -Enable}
if($psSession)
{
    Remove-PSSession $psSession
}
```

**Parancsfájl telemetria letiltása:**

```powershell
$ip = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.
$pwd= ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $pwd)
$psSession = New-PSSession -ComputerName $ip -ConfigurationName PrivilegedEndpoint -Credential $cred
Invoke-Command -Session $psSession {Set-Telemetry -Disable}
if($psSession)
{
    Remove-PSSession $psSession
}
```

## <a name="next-steps"></a>További lépések

- [Az Azure verem development kit központi telepítési csomag](https://azure.microsoft.com/overview/azure-stack/try/?v=try)

- [Azure verem szoftverfejlesztői készlet telepítése](azure-stack-run-powershell-script.md)
