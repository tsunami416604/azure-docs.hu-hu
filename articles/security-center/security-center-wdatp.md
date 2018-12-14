---
title: A Windows Defender komplex veszélyforrások elleni védelem az Azure Security Centerrel
description: Ez a dokumentum bemutatja az integráció az Azure Security Center és a Windows Defender komplex veszélyforrások elleni védelem között.
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
ms.date: 09/20/2018
ms.author: barclayn
ms.openlocfilehash: 1109e72d00ccddd6fc0f120ee71c88adaae72fed
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2018
ms.locfileid: "53337597"
---
# <a name="windows-defender-advanced-threat-protection-with-azure-security-center"></a>A Windows Defender komplex veszélyforrások elleni védelem az Azure Security Centerrel

Az Azure Security Center integrálásával kiterjeszti a Felhőbeli számítási feladatok védelmét platformok nyújthassa [Windows Defender komplex veszélyforrások elleni védelem](https://www.microsoft.com/en-us/WindowsForBusiness/windows-atp) (ATP).
Ez a változás végpont észlelés és válasz (EDR) funkciókészletet biztosít. A Windows Defender ATP-integráció felismerése rendellenességeket. Is észleli, és az Azure Security Center által figyelt kiszolgálói végpontot a speciális támadások válaszolni.

Az Azure Security Center-ügyfelek már az funkcióit a Windows Defender ATP-ben:

- **Következő generációs utáni incidensek észlelési érzékelők**: Windows-kiszolgálók a Windows Defender ATP-érzékelő gyűjtsön viselkedési jelek hatalmas választékát.

- **Analytics-alapú, felhőalapú post illetéktelen behatolás-észlelési**: A Windows Defender ATP gyorsan alkalmazkodik a változó fenyegetések. Fejlett elemzés és big data használ. A Windows Defender ATP egészíteni együtt az Intelligent Security Graph hatékonyságával Windows, az Azure és Office ismeretlen fenyegetések észleléséhez. Gyakorlatban hasznosítható riasztásokat biztosít, és lehetővé teszi, hogy gyorsan reagálhat.

- **Fenyegetések felderítése**: A Windows Defender ATP azonosítja a támadó eszközök technikák és eljárásokat. Ha ezek észlel, riasztásokat állít elő. Microsoft threat vadászok által létrehozott adatokat és biztonsági csapatok, partnerek által biztosított kiegészítve használ.

Ezek a képességek, mostantól elérhetők az Azure Security Center:

- **Bevezetési automatikus**: A Windows Defender ATP-érzékelő Windows kiszolgálók előkészítve az Azure Security Center automatikusan engedélyezve lesz.

- **Egyetlen**: Az Azure Security Center konzolján a Windows Defender ATP riasztásokat jeleníti meg.

- **Részletes vizsgálat gép**: Az Azure Security Center-ügyfelek hozzáférhetnek a Windows Defender ATP konzol végrehajtásához a részletes vizsgálat elvégzésével nyújt betekintést a hatókör, a támadásokról.

![Az Azure Security Center, riasztások és az egyes riasztásokkal kapcsolatos általános információk megjelenítése](media/security-center-wdatp/image1.png)

Is [a riasztás kivizsgálásához](security-center-investigation.md) az Azure Security Centerben:

![A riasztási vizsgálati irányítópult az Azure Security Centerben](media/security-center-wdatp/image2.png)

A riasztás további megvizsgálhatja a Windows Defender ATP alapján történő kimutatás. Nincs látható további információk, például a riasztási folyamat fanézetben, és az incidens grafikon. Megtekintheti a részletes machine ütemterv legfeljebb hat hónappal korábbi ideig minden viselkedés megjelenítő is.

![Riasztásokkal kapcsolatos részletes információk a Windows Defender ATP lap](media/security-center-wdatp/image3.png)

## <a name="platform-support"></a>Platformtámogatás

Ez a funkció a Windows Server 2012 R2 és Windows Server 2016 észlelési támogatja.

Csak azokat a kiszolgálókat, az előfizetések esetében a standard szolgáltatásszinten támogatottak.

## <a name="onboarding-instructions"></a>Előkészítési utasítások

Ha engedélyezve van a Windows Defender ATP-integráció megtekintéséhez jelölje ki **a Security center** > **biztonsági házirend** > **előfizetés**  >  **Beállításainak szerkesztése**.

  ![Az Azure Security Center Szabályzatkezelés](media/security-center-wdatp/policy-management.png)

Itt látható a jelenleg engedélyezett való integrációt.

  ![Az Azure Security Center Threat detection beállítások lap engedélyezve van a Windows Defender ATP-integráció](media/security-center-wdatp/enable-integrations.png)

- Ha már előkészítve a kiszolgálók az Azure Security Center standard szintű, kell nem tesz semmit további. Az Azure Security Center a rendszer automatikusan készítse elő a kiszolgálók a Windows Defender ATP-ben. Ez akár 24 óráig is eltarthat.

- Ha soha nem előkészítve a kiszolgálók az Azure Security Center standard szintű, készítse elő azokat az Azure Security Center a szokott módon.

- Ha előkészítve a Windows Defender ATP keresztül kiszolgálók:
  - Esetében tekintse meg az útmutató a [regisztrációjának megszüntetésére szerverek hogyan](https://go.microsoft.com/fwlink/p/?linkid=852906).
  - Előkészítése az Azure Security Center ezeken a kiszolgálókon.

## <a name="access-to-the-windows-defender-atp-portal"></a>A Windows Defender ATP portáljához való hozzáférésre

Kövesse a [felhasználói hozzáférések hozzárendelése a portál](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/assign-portal-access-windows-defender-advanced-threat-protection).

## <a name="set-the-firewall-configuration"></a>A tűzfal-konfiguráció beállítása

Ha a proxy vagy tűzfal blokkolja a névtelen forgalmat, a Windows Defender ATP-érzékelő csatlakozik a rendszer környezetéből, győződjön meg arról, hogy névtelen forgalom engedélyezett. Kövesse a [engedélyezze a hozzáférést a Windows Defender ATP szolgáltatás URL-címeinek a proxykiszolgálón](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/configure-proxy-internet-windows-defender-advanced-threat-protection#enable-access-to-windows-defender-atp-service-urls-in-the-proxy-server).

## <a name="test-the-feature"></a>A szolgáltatás tesztelése

A Windows Defender ATP jóindulatú tesztriasztás generálása:

1. Távoli asztali eléréséhez, vagy egy Windows Server 2012 R2 virtuális Gépet, vagy egy Windows Server 2016 virtuális gép használja.  Nyisson meg egy parancssori ablakot.

2. Amikor a rendszer kéri másolja, és futtassa a következő parancsot. A parancssori ablakban automatikusan bezáródik.

    ```
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-WDATP-test\\invoice.exe'); Start-Process 'C:\\test-WDATP-test\\invoice.exe'
    ```

   ![Egy parancssori ablakot a fenti paranccsal](media/security-center-wdatp/image4.jpeg)

3. A parancs végrehajtása sikeres, ha az Azure Security Center irányítópultjának és a Windows Defender ATP portáljának egy új riasztás megjelenik. Ez a riasztás jelenik meg néhány percet is igénybe vehet.

4. Tekintse át a riasztás a Security Centerben, lépjen a **biztonsági riasztások** >  **gyanús Powershell CommandLine**.

5. A vizsgálat ablakban jelölje ki a hivatkozásra kattintva nyissa meg a Windows Defender ATP portálján.

## <a name="next-steps"></a>További lépések

- [Biztonsági szabályzatok beállítása az Azure Security Center](tutorial-security-policy.md): Ismerje meg, hogyan konfigurálhat biztonsági házirendeket az Azure-előfizetések és -erőforráscsoportok.
- [Biztonsági javaslatok kezelése az Azure Security Center](security-center-recommendations.md): Megtudhatja, hogyan javaslatok az Azure-erőforrások védelme.
- [Biztonsági állapotfigyelés az Azure Security Center](security-center-monitoring.md): Az Azure-erőforrások állapotának figyelését ismertető útmutató.
