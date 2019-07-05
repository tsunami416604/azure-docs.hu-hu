---
title: A Windows Defender komplex veszélyforrások elleni védelem az Azure Security Centerrel
description: Ez a dokumentum bemutatja az integráció az Azure Security Center és a Windows Defender komplex veszélyforrások elleni védelem között.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/27/2018
ms.author: monhaber
ms.openlocfilehash: 1737856067b2490db4a993b4383b320cb13a7774
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2019
ms.locfileid: "67551768"
---
# <a name="windows-defender-advanced-threat-protection-with-azure-security-center"></a>A Windows Defender komplex veszélyforrások elleni védelem az Azure Security Centerrel

Az Azure Security Center kiterjeszti a Cloud Workload Protection Platforms ajánlatát a [Windows Defender Advanced Threat Protection](https://www.microsoft.com/en-us/WindowsForBusiness/windows-atp) (ATP) integrálásával.
Ez a változás átfogó végpontészlelési és -válaszolási (EDR) képességeket tesz lehetővé. A Windows Defender ATP-integráció felismerése rendellenességeket. Is észleli, és az Azure Security Center által figyelt kiszolgálói végpontot a speciális támadások válaszolni.

## <a name="windows-defender-atp-features-in-security-center"></a>A Windows Defender ATP-funkciók a Security Centerben

A Windows Defender ATP használata esetén kap:

- **Következő generációs utáni incidensek észlelési érzékelők**: Windows-kiszolgálók a Windows Defender ATP-érzékelő gyűjtsön viselkedési jelek hatalmas választékát.

- **Analytics-alapú, felhőalapú post illetéktelen behatolás-észlelési**: A Windows Defender ATP gyorsan alkalmazkodik a változó fenyegetések. Fejlett elemzés és big data használ. A Windows Defender ATP egészíteni együtt az Intelligent Security Graph hatékonyságával Windows, az Azure és Office ismeretlen fenyegetések észleléséhez. Gyakorlatban hasznosítható riasztásokat biztosít, és lehetővé teszi, hogy gyorsan reagálhat.

- **Fenyegetések felderítése**: A Windows Defender ATP azonosítja a támadó eszközök technikák és eljárásokat. Ha ezek észlel, riasztásokat állít elő. Microsoft threat vadászok által létrehozott adatokat és biztonsági csapatok, partnerek által biztosított kiegészítve használ.

Ezek a funkciók mostantól elérhetők az Azure Security Centerben:

- **Bevezetési automatikus**: A Windows Defender ATP-érzékelő Windows kiszolgálók előkészítve az Azure Security Center automatikusan engedélyezve lesz.

- **Egyetlen**: Az Azure Security Center konzolján a Windows Defender ATP riasztásokat jeleníti meg.

- **Részletes vizsgálat gép**: Az Azure Security Center-ügyfelek hozzáférhetnek a Windows Defender ATP konzol végrehajtásához a részletes vizsgálat elvégzésével nyújt betekintést a hatókör, a támadásokról.

![Az Azure Security Center, riasztások és az egyes riasztásokkal kapcsolatos általános információk megjelenítése](media/security-center-wdatp/image1.png)

A riasztás további megvizsgálhatja a Windows Defender ATP alapján történő kimutatás. Nincs látható további információk, például a riasztási folyamat fanézetben, és az incidens grafikon. Megtekintheti a részletes machine ütemterv legfeljebb hat hónappal korábbi ideig minden viselkedés megjelenítő is.

![Riasztásokkal kapcsolatos részletes információk a Windows Defender ATP lap](media/security-center-wdatp/image3.png)

## <a name="platform-support"></a>Platformtámogatás

A Windows Defender ATP-ben a Security Center észlelési egy standard szintű service-előfizetések alá tartozó Windows Server 2012 R2 és Windows Server 2016 operációs rendszerek használatát támogatja.

> [!NOTE]
> Kiszolgálók figyelése az Azure Security Center, ha a Windows Defender ATP-bérlő automatikusan létrejön, és a Windows Defender ATP-adatok Európában tárolt alapértelmezés szerint. Ha az adatok áthelyezése egy másik helyre van szüksége, forduljon a Microsoft Support alaphelyzetbe állítja a bérlő szeretné.

## <a name="onboarding-servers-to-security-center"></a>A Security Center bevezetése-kiszolgálók 

A kiszolgálók felvétele a Security centerhez, kattintson a **nyissa meg az Azure Security Center előkészítése kiszolgálók** , a Windows Defender ATP-kiszolgáló bevezetése.

1. Az a **bevezetési** panelen válassza ki vagy hozzon létre egy munkaterületet, amely tárolja az adatokat. <br>
2. Ha nem látja az összes munkaterületet, akkor előfordulhat, hogy engedélyek hiánya miatt, győződjön meg arról, hogy a munkaterület az Azure Security Standard szintre van beállítva. További információ: [a Security Center Standard csomagra váltásával fokozott biztonságot érhet](security-center-pricing.md).
    
3. Válassza ki **kiszolgálók hozzáadása** vonatkozó útmutatás megtekintéséhez a Microsoft Monitoring Agent telepítése. 

4. Az előkészítés, után figyelemmel kísérheti a gépek **számítási és az alkalmazások**.

   ![Számítógépek felvétele](media/security-center-wdatp/onboard-computers.png)

## <a name="enable-windows-defender-atp-integration"></a>A Windows Defender ATP-integráció engedélyezése

Ha engedélyezve van a Windows Defender ATP-integráció megtekintéséhez jelölje ki **a Security center** > **díjszabási & beállítások** > kattintson az előfizetésre.

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

Ha a proxy vagy tűzfal blokkolja a névtelen forgalmat, a Windows Defender ATP-érzékelő csatlakozik a rendszer környezetéből, győződjön meg arról, hogy névtelen forgalom engedélyezett. Kövesse a [engedélyezze a hozzáférést a Windows Defender ATP szolgáltatás URL-címeinek a proxykiszolgálón](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/configure-proxy-internet-windows-defender-advanced-threat-protection#enable-access-to-microsoft-defender-atp-service-urls-in-the-proxy-server).

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
