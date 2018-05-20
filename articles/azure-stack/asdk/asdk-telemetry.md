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
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/17/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: bfd16901c5ce036719a1ed19e9a5b5c6ef52be93
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2018
---
# <a name="azure-stack-telemetry"></a>Az Azure verem telemetriai adat

Azure verem rendszeradatok vagy telemetriai adatokat automatikusan feltöltése a Microsoftnak a kapcsolódó felhasználói élményt keresztül. Azure verem telemetriai adatokat a gyűjtött adatokat arra szolgál, elsősorban a felhasználói élmény javítása érdekében a Microsoft csapatai által, valamint a biztonsági, rendszerállapot, minőségét és teljesítményének elemzése.

Azure verem kezelőként telemetriai vállalati központi telepítéshez értékes információkat biztosíthat, és lehetővé teszi az hangot, amelyek segítségével Azure verem alakzat jövőbeli verzióiban.

> [!NOTE]
> Azure verem beállítható is úgy, hogy Azure előre használati adatok a számlázáshoz. Ez elengedhetetlen ahhoz fizetési,-akkor-használható számlázási többcsomópontos Azure verem felhasználók. Használati jelentésben egymástól függetlenül vezérlik a telemetriai adatokból, és nincs szükség, válassza ki a kapacitás modelljét többcsomópontos felhasználókat vagy Azure verem szoftverfejlesztői készlet felhasználókhoz. Ezek a forgatókönyvek a használati jelentésben kikapcsolható [a regisztrációs parancsfájlt](https://docs.microsoft.com/azure/azure-stack/azure-stack-usage-reporting).

Azure verem telemetriai alapul, a Windows Server 2016 kapcsolódó felhasználói felület- és Telemetriabevitelt összetevő, amely használja a [esemény Windows (nyomkövetés)](https://msdn.microsoft.com/library/dn904632(v=vs.85).aspx) nyomkövetési naplózás technológia gyűjtse össze és tárolja a telemetriai események és adatait. Azure verem összetevők események és gyűjti az adatokat nyilvános operációs rendszer eseménynaplózás és API-k nyomkövetési adatok közzététele naplózási ugyanazt a technológiát használja. Azure verem összetevők például a hálózati erőforrás-szolgáltató, a Storage erőforrás-szolgáltató, a figyelés erőforrás-szolgáltató és a frissítés erőforrás-szolgáltató. A kapcsolódó felhasználói élményt és Telemetriai összetevő adatok SSL használatával titkosítja, és használja a tanúsítvány rögzítését küldött telemetriai adatok HTTPS-KAPCSOLATON keresztül a Microsoft Data Management szolgáltatáshoz.

> [!NOTE]
> Telemetriai adatok folyamat támogatásához port 443-as (HTTPS) nyissa meg a hálózaton kell lennie. A kapcsolódó felhasználói élményt és Telemetriai összetevő kapcsolódik a Microsoft adatkezelés szolgáltatás https://v10.vortex-win.data.microsoft.com. A kapcsolódó felhasználói élményt és Telemetriai összetevő is csatlakozik https://settings-win.data.microsoft.com konfigurációs információk letöltése.

## <a name="privacy-considerations"></a>Adatvédelmi megfontolások
Az ETW-szolgáltatás telemetriai adatokat továbbítja védett felhőbeli tárhelyén. A telemetriai adatok elérésére minimális jogosultságokkal rendelkező útmutatók elvét. Csak Microsoft személyek érvényes üzleti van szükség a telemetriai adataihoz való hozzáférés engedélyezett. A Microsoft nem osztja meg személyes adatok harmadik felekkel ügyfeleink kivételével az ügyfél belátása vagy leírt korlátozott céljából a [Azure verem adatvédelmi nyilatkozat](https://privacy.microsoft.com/PrivacyStatement). Azt megosztása üzleti jelentések számítógépgyártók és a partnerek, amely tartalmazza az összesített, anonimizált telemetriai adatokat. Egy belső Microsoft team, beleértve az adatokat, adatvédelmi és jogi felügyeleti érdekelt felek adatmegosztás döntések hozhatók.

Microsoft a tartja, és információt minimalizálásáról eljárásokat. Igyekszünk összegyűjteni csak a igazolnia kell, és tároljuk, csak addig, szükség szerint biztosítani a szolgáltatást, vagy elemzés céljából. Hogyan működik az Azure verem rendszer és az Azure-szolgáltatásokkal kapcsolatos információk nagy részét a rendszer törli hat hónapban. Foglalja össze, vagy összesített adatai a hosszabb ideig tartanak.

Tudjuk, hogy az adatvédelmi és a felhasználók adatok biztonsága fontos. A gondos és átfogó megközelítés átirányította ügyfelek adatainak és az Azure veremnek megfelelő felhasználói adatok védelmét. A rendszergazdák vezérlők szolgáltatásait és adatvédelmi beállítások testreszabásához bármikor rendelkezik. A kötelezettségvállalás átláthatóság és a megbízhatóság az törlése:
- Folyamatban, nyissa meg az ügyfelekkel kapcsolatos adatokat gyűjt a Microsoft típusú.
- Vállalati ügyfelek azt be vezérlő – azok a saját adatvédelmi beállítások is testre szabhatók.
- A Microsoft kerülnek ügyfél adatvédelmi és biztonsági előre.
- Dolgozunk a transzparens módjáról telemetriai adatainak beolvasása.
- Telemetria felhasználói élmény javítására használjuk.

A Microsoft nem tervezi a bizalmas adatokat, például a hitelkártyaszámukat, felhasználónevek és jelszavak, e-mail címek vagy más hasonló módon bizalmas adatokat gyűjt. Ha azt állapítja meg, hogy bizalmas információk véletlenül érkezett, hogy törölje a parancsikont.

## <a name="examples-of-how-microsoft-uses-the-telemetry-data"></a>Példák a telemetriai adatok Microsoft általi használatára
Telemetria gyorsan azonosíthatja, és javítsa ki a kritikus megbízhatóságával kapcsolatos problémákat a felhasználók központi telepítések és konfigurációk segített a fontos szerepet játszik. A telemetriai adatok, amelyek azt összegyűjtése betekintést segítsen szolgáltatásokat vagy hardverkonfigurációk során problémák gyors felismerését. Ezek az adatok beolvasása az ügyfelek és a meghajtó fejlesztései az ökoszisztéma be a Microsoft képes segít az integrált Azure verem megoldásainkkal minőségének sáv ablakába.

Telemetria teszi a Microsoft jobban megérteni, hogyan telepítsék központilag az ügyfél összetevők, használatát és az üzleti célok eléréséhez használhatják a szolgáltatásokat. Nyerek betekintést az adatokba, hogy az adatok segítségével rangsorolhatja mérnöki beruházások értékét, hogy a felhasználók élményeit és munkaterhelések közvetlen hatással lehet területeken.

Például az ügyfél használati tárolók, tárolási és hálózati konfigurációk társított Azure verem szerepkörök. A meghajtó és az egyes a kezelési és figyelési megoldások eszközintelligencia elemzéseket is használjuk. Segítségével az ügyfelek minőségi eseményadatokat, és a Mentés azáltal, hogy kevesebb támogatási pénz meghívja a Microsoftnak.

## <a name="manage-telemetry-collection"></a>Telemetria gyűjtemény kezelése
Nem ajánlott kikapcsolni a telemetriai adatokat a szervezetében, telemetriai adatokat adja meg, hogy a javított termékfunkciók és stabilitását meghajtók adatait. Felismertük, hogy bizonyos esetekben erre akkor lehet szükség.

Ezek a példányok konfigurálhatók a beállításjegyzékbeli beállítások központi telepítés előtti használatával, vagy a telemetriai adatok végpontok feladás egy vagy több központi telepítéssel a Microsoftnak küldött telemetriai szint.

### <a name="set-telemetry-level-in-the-windows-registry"></a>Telemetria szint beállítása a Windows beállításjegyzékben
A Windows Beállításszerkesztőt segítségével manuálisan telemetriai szintjének beállítása a fizikai állomáson Azure verem üzembe helyezése előtt. Ha a kezelési házirendek már létezik, például a csoportházirend felülírja a beállításjegyzék-beállítást.

Azure verem a development kit gazdagépen való telepítése előtt a CloudBuilder.vhdx indul, és egy emelt szintű PowerShell-ablakban futtassa az alábbi parancsfájlt:

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

A telemetriai adatok szintek a következők: összegző és kategorizált be négy szint (0 – 3):

**0 (biztonság)**. Csak biztonsági adatokat. Biztonságos, amely az operációs rendszer megơrzéséhez szükséges információk, többek között a kapcsolódó felhasználói élményt és Telemetriai összetevő beállításait és a Windows Defender adatait. Nincs Azure-verem adott telemetriai kibocsátott ezen a szinten.

**1 (alapszintű)**. A biztonsági adatokat, és alapvető állapot és minőség adatok. Alapvető eszköz-információkat, beleértve: minőségi kapcsolatos adatokat, alkalmazás kompatibilitási, alkalmazás használati adatok és a biztonsági szint adatait. A telemetriai adatok szint alapvető lehetővé teszi, hogy Azure verem telemetriai adatokat. Ezen a szinten gyűjtött adatokat tartalmazza:

- **Alapvető eszköz adatai** , hogy segít biztosítani, típusok és a rendszert eredményez, natív és virtuális Windows Server 2016 példánya konfigurációkkal kapcsolatos megértéséhez többek között:
 - Gép attribútumait, például a gyártó, a modell,
 - Hálózatkezelés attribútumait, például a számát és sebességét, a hálózati adapterek
 - Processzor és memória attribútumait, például a magok száma, memória mérete
 - Tárolási attribútumok, például a meghajtók, típusát és méretét.
- **Telemetria funkció**, többek között a következőket feltöltött események, az eldobott események és az utolsó százaléka feltöltés ideje.
- **Minőségi kapcsolatos információ** , amely segít a Microsoft fejlesztése alapszinten megértse, hogyan Azure verem hajt végre. Példa: hardverkonfigurációhoz a kritikus riasztások száma.
- ** Kompatibilitási adatokat, amely segítséget nyújt arról, hogy mely erőforrás-szolgáltató a rendszer és a virtuális gép van telepítve, és azonosítja az esetleges kompatibilitási problémák megértése.

**2 (speciális)**. További betekintést nyerjen, többek között: az operációs rendszer, és más Azure-verem szolgáltatások használatának módját, hogyan működnek, speciális megbízhatósági adatok és az alapszintű és a biztonsági szintek adatait.

**3 (teljes)**. Minden adatok azonosítására és a problémák elhárításának támogatása szükséges, illetve a a **biztonsági**, **alapvető**, és **bővített** szintek.

> [!NOTE]
> Az alapértelmezett telemetriai szintű értéke 2 (speciális).

Windows és az Azure-verem telemetria kikapcsolása letiltása SQL telemetriai adatokat. A Windows Server telemetriai beállítások következményeit kapcsolatos további információkért lásd a [Windows Telemetriai tanulmány](https://aka.ms/winservtelemetry).

> [!IMPORTANT]
> A telemetriai adatok szinteket csak a Microsoft Azure verem összetevők vonatkoznak. A felhőszolgáltatások kívül telemetriai szinten kommunikálhatnak, nem a Microsofttól származó szoftverek összetevők és az Azure verem hardverszállító partnerétől a hardver életciklus állomáson futó szolgáltatások. Az Azure-verem megoldás hardverszolgáltatót telemetriai szabályzatát, és hogyan részt vevő, vagy nem vesznek részt együtt kell működnie.

### <a name="enable-or-disable-telemetry-after-deployment"></a>Engedélyezheti vagy tilthatja le a telemetriai adatokat a központi telepítést követően

Letiltása és engedélyezése telemetriai telepítés után, akkor hozzáféréssel kell rendelkeznie a a Rendszerjogosultságú végpont (EGP) amely a virtuális gépeken ERCS hagyja.
1.  Engedélyezése: `Set-Telemetry -Enable`
2.  Letiltása: `Set-Telemetry -Disable`

A paraméter részletei:
> . A paraméter engedélyezése – bekapcsolásához válassza a telemetriai adatok feltöltése

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
[Adja hozzá a Piactéri elemet](asdk-marketplace-item.md)
