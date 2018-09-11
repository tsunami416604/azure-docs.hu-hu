---
title: A Windows Defender komplex veszélyforrások elleni védelem (ATP) az Azure Security Center (nyilvános előzetes verzió) |} A Microsoft Docs
description: Ez a dokumentum bemutatja az integráció az Azure Security Center és a Windows Defender ATP között.
services: security-center
documentationcenter: na
author: barclayn
manager: MBaldwin
editor: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/06/2018
ms.author: barclayn
ms.openlocfilehash: f2cb4edd469d76f79e4134ca261bac5263bf3ce4
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2018
ms.locfileid: "44296057"
---
# <a name="windows-defender-advanced-threat-protection-atp-with-azure-security-center-public-preview"></a>A Windows Defender komplex veszélyforrások elleni védelem (ATP) az Azure Security Center (nyilvános előzetes verzió)

Az Azure Security Center integrálásával kiterjeszti a Felhőbeli számítási feladatok védelmét platformok (CWPP) nyújthassa [Windows Defender ATP](https://www.microsoft.com/WindowsForBusiness/windows-atp).
Ez a változás végpont észlelés és válasz (EDR) funkciókészletet biztosít. Ez lehetővé teszi a helyszíni rendellenességeket, észlelését és elhárítását az ASC által figyelt kiszolgálói végpontot a speciális támadások.

Az Azure Security Center-ügyfelek mostantól a Windows Defender ATP funkciók is használhatók:

- **Következő – általános utáni incidensek észlelési érzékelők:** Windows-kiszolgálók hatalmas választékának viselkedési jelek, engedélyezze a speciális támadások észlelése és kivizsgálása érdekében gyűjti a Windows Defender ATP-érzékelő.

- **Analytics-alapú, felhőalapú utáni incidensek észlelése:** Windows Defender ATP gyorsan alkalmazkodik a változó fenyegetések. Fejlett elemzés és big data alkalmaz. Az Intelligent Security Graph-jelek hatékonyságát, akkor egészíteni, Windows, az Azure és Office ismeretlen fenyegetések észleléséhez. Gyakorlatban hasznosítható riasztásokat biztosít, és lehetővé teszi, hogy gyorsan reagálhat.

- **Fenyegetések felderítése**: a Windows Defender ATP azonosítja a támadó eszközök technikák és eljárásokat, és riasztásokat állít elő, ha ezek jelennek meg. Microsoft vadászok, biztonsági csoportokat, hozza létre, és a partnerek által biztosított a fenyegetésfelderítés kibővített adatokat használ.

Ezek a képességek, mostantól elérhetők az Azure Security Center:

- Windows-kiszolgálók, amelyek az ASC-hez felkészített automatikusan engedélyezve van az automatikus előkészítés – a Windows Defender ATP-érzékelő

- Egyetlen panelen az ASC - tekinthesse Windows Defender ATP-riasztások állnak rendelkezésre az ASC-konzolon

- Részletes vizsgálat machine - ASC ügyfelek elérhetik a Windows Defender ATP konzol használatával hajtja végre a részletes vizsgálat elvégzésével nyújt betekintést az illetéktelen behatolás hatóköre

![*. Ábra – 1 jelennek meg a teljes kép során, beleértve az ASC * által generált riasztások vizsgálata](media/security-center-wdatp/image1.png)

Segítségével megvizsgálhatja, a riasztás az Azure Security Centerben:

![2. ábra vizsgálat – az Azure Security Center](media/security-center-wdatp/image2.png)

A riasztás további megvizsgálhatja a Windows Defender ATP alapján történő kimutatás. Nincs látható további információk, például a riasztási folyamat fanézetben, incidens gráfokat és részletes machine ütemterv adatokhoz hozzáférést biztosító összes viselkedések legfeljebb hat hónappal korábbi időszakra.

![Ábra 3 vizsgálat – a Windows Defender ATP](media/security-center-wdatp/image3.png)

## <a name="platform-support"></a>Platformtámogatás

Ez a funkció a Windows Server 2012 R2 és Windows Server 2016 észlelési támogatja.

Csak a Standard szintű előfizetésekhez kiszolgálók

## <a name="onboarding-instructions"></a>Előkészítési utasítások

- Ha Ön már előkészítve az ASC ASC standard szintre - semmilyen műveletet a kiszolgálók szükséges, automatikusan az előkészítésére WDATP-kiszolgálókat.

- Ha Ön soha nem a kiszolgálók ASC standard díjcsomag – készítse elő az ASC-hez a szokásos módon előkészítve.

- Ha előkészített a kiszolgálók WDATP keresztül:
  - Esetében tekintse meg az útmutató a [regisztrációjának megszüntetésére szerverek hogyan](https://go.microsoft.com/fwlink/p/?linkid=852906).
  - Előkészítheti az ASC-hez

## <a name="access-to-wdatp-portal"></a>WDATP portáljához való hozzáférésre

Kövesse az [felhasználói hozzáférések hozzárendelése a portálon](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/assign-portal-access-windows-defender-advanced-threat-protection)

## <a name="set-firewall-configuration"></a>Tűzfal-konfiguráció beállítása

Ha a proxy vagy tűzfal blokkolja a névtelen forgalmat, a Windows Defender ATP-érzékelő a rendszerkörnyezetben csatlakozik, ellenőrizze, hogy névtelen forgalom engedélyezett; kövesse az utasításokat [a proxykiszolgálót a Windows Defender ATP szolgáltatás URL-címek hozzáférésének engedélyezése](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/configure-proxy-internet-windows-defender-advanced-threat-protection#enable-access-to-windows-defender-atp-service-urls-in-the-proxy-server)

## <a name="how-can-i-test-the-feature"></a>Hogyan tesztelheti a szolgáltatást?

 Az alábbi lépéseket egy ártalmatlan WDATP teszt riasztást generál.

1. A Windows Server rendszerű virtuális gépek közül távoli asztali eléréséhez (2012 R2 vagy 2016) az előfizetésben, és nyissa meg egy parancssori ablakot

2. Amikor a rendszer kéri másolja, és futtassa az alábbi parancsot. Vegye figyelembe, hogy a parancssori ablakban automatikusan bezáródik.

    **PowerShell.exe - NoExit - ExecutionPolicy Mellőzés - je hodnota allowstransparency Nastavena rejtett (New-Object System.Net.WebClient). DownloadFile ("http://127.0.0.1/1.exe", "C:\\test-WDATP-test\\invoice.exe"); Folyamatának elindítása "C:\\test-WDATP-test\\invoice.exe a(z) **

  ![a fenti parancsot egy parancssori ablakot képe](media/security-center-wdatp/image4.jpeg)

3. Ha sikeres, egy új riasztás ASC és WD ATP portálján néhány perc múlva fog megjelenni.

4. Tekintse át a riasztás az Azure Security Centerben, ugorjon a **biztonsági riasztások –\> gyanús Powershell-parancssor**

5. A vizsgálat alapján az ablakban kattintson a hivatkozásra WDATP portál átirányítása

## <a name="next-steps"></a>További lépések

* [Biztonsági szabályzatok beállítása az Azure Security Centerben](security-center-policies.md) – Ez a cikk bemutatja, hogyan konfigurálhat biztonsági házirendeket Azure-előfizetései és -erőforráscsoportjai számára.
* [Biztonsági javaslatok kezelése az Azure Security Center](security-center-recommendations.md) – megtudhatja, hogyan javaslatok az Azure-erőforrások védelme.
* [Biztonsági állapotfigyelés az Azure Security Center](security-center-monitoring.md) – útmutató az Azure-erőforrások állapotának monitorozásához.
