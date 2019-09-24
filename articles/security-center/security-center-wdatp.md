---
title: Windows Defender komplex veszélyforrások elleni védelem Azure Security Center
description: Ez a dokumentum bemutatja a Azure Security Center és a Windows Defender komplex veszélyforrások elleni védelem közötti integrációt.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/21/2019
ms.author: memildin
ms.openlocfilehash: b1af4f99b1446599f53448884d3147601fba56a8
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/23/2019
ms.locfileid: "71200650"
---
# <a name="windows-defender-advanced-threat-protection-with-azure-security-center"></a>Windows Defender komplex veszélyforrások elleni védelem Azure Security Center

Az Azure Security Center kiterjeszti a Cloud Workload Protection Platforms ajánlatát a [Windows Defender Advanced Threat Protection](https://www.microsoft.com/en-us/WindowsForBusiness/windows-atp) (ATP) integrálásával.
Ez a változás átfogó végpontészlelési és -válaszolási (EDR) képességeket tesz lehetővé. A Windows Defender ATP-integrációval szokatlanokat lehet kimutatni. A Azure Security Center által figyelt kiszolgálói végpontokon a speciális támadások észlelésére és reagálására is lehetőség van.

## <a name="windows-defender-atp-features-in-security-center"></a>A Windows Defender ATP szolgáltatásai Security Center

Ha a Windows Defender ATP-t használja, a következőket kapja:

- **Következő generációs post-észlelési érzékelők**: A Windows Defender ATP-érzékelők a Windows-kiszolgálókon a viselkedési jelek nagy tömbjét gyűjtik össze.

- **Elemzésen alapuló, felhőalapú utólagos szabálysértés észlelése**: A Windows Defender ATP gyorsan alkalmazkodik a fenyegetések változásához. Fejlett elemzési és big dataeket használ. A Windows Defender ATP-t az ismeretlen fenyegetések észlelése érdekében a Intelligens biztonsági gráf, valamint a Windows, az Azure és az Office közötti jelekkel fokozza. Kezelhető riasztásokat biztosít, és lehetővé teszi a gyors reagálást.

- **Fenyegetés intelligencia**: A Windows Defender ATP azonosítja a támadó eszközöket, technikákat és eljárásokat. Ha észleli ezeket, riasztásokat hoz létre. A Microsoft Threat Hunters és a biztonsági csapatok által létrehozott, a partnerek által biztosított intelligenciával kiegészített adatok használatával működik.

Ezek a funkciók mostantól elérhetők az Azure Security Centerben:

- **Automatizált**előkészítés: A Windows Defender ATP-érzékelő automatikusan engedélyezve van a Azure Security Centerba beépített Windows-kiszolgálókon.

- **Önálló**üvegtábla: A Azure Security Center-konzolon a Windows Defender ATP-riasztások jelennek meg.

- **Részletes gépi vizsgálat**: Azure Security Center ügyfelek hozzáférhetnek a Windows Defender ATP-konzolhoz, és részletes vizsgálatot végezhetnek a szabálysértés hatókörének felderítése érdekében.

![Azure Security Center a riasztások listájának megjelenítése és az egyes riasztások általános információi](media/security-center-wdatp/image1.png)

A riasztást a Windows Defender ATP-re való Rámutatás révén tovább vizsgálhatja. Itt további információk jelenhetnek meg, például a riasztási folyamat fája és az incidens gráf. Megtekintheti a részletes gépi idővonalat is, amely egy 6 hónapos időszakra visszamenőlegesen mutatja be az összes viselkedést.

![Windows Defender ATP-oldal részletes információkkal a riasztásról](media/security-center-wdatp/image3.png)

## <a name="platform-support"></a>Platformtámogatás

A Windows Defender ATP a Security Center támogatja a Windows Server 2016, 2012 R2 és 2008 R2 SP1 operációs rendszerek észlelését a standard szintű szolgáltatás előfizetésében.

> [!NOTE]
> Ha Azure Security Centert használ a kiszolgálók figyelésére, a rendszer automatikusan létrehoz egy Windows Defender ATP-bérlőt, és a Windows Defender ATP-adatvédelmet alapértelmezés szerint Európában tárolja. Ha át kell helyeznie az adatait egy másik helyre, kapcsolatba kell lépnie Microsoft ügyfélszolgálata a bérlő alaphelyzetbe állításához.

## <a name="onboarding-servers-to-security-center"></a>Kiszolgálók bevezetése Security Center 

A kiszolgálók Security Centerbe való bevezetéséhez kattintson az **Ugrás gombra Azure Security Center** a Windows Defender ATP-kiszolgáló előkészítési kiszolgálóinak bevezetéséhez.

1. A bevezetési panelen válasszon ki vagy hozzon létre egy munkaterületet, amelyben az adattárolást tárolja. <br>
2. Ha nem látja az összes munkaterületet, akkor az engedélyek hiánya miatt előfordulhat, hogy a munkaterület az Azure Security Standard csomagra van beállítva. További információ: a [frissítés a Security Center Standard szintjére a fokozott biztonság](security-center-pricing.md)érdekében.
    
3. Válassza a **kiszolgálók hozzáadása** lehetőséget a Microsoft monitoring Agent telepítésére vonatkozó utasítások megtekintéséhez. 

4. A bevezetést követően nyomon követheti a gépeket a **számítás és az alkalmazások**területen.

   ![Számítógépek előkészítése](media/security-center-wdatp/onboard-computers.png)

## <a name="enable-windows-defender-atp-integration"></a>A Windows Defender ATP-integráció engedélyezése

Ha meg szeretné tekinteni, hogy engedélyezve van-e a Windows Defender ATP-integrációja, válassza a **Security Center** > **díjszabása & beállításokat** > kattintson az előfizetésre.

  ![Azure Security Center házirend kezelése](media/security-center-wdatp/policy-management.png)

Itt láthatja az aktuálisan engedélyezett integrációkat.

  ![Azure Security Center veszélyforrások észlelésére vonatkozó beállítások lap, amelyen engedélyezve van a Windows Defender ATP-integráció](media/security-center-wdatp/enable-integrations.png)

- Ha már előkészítette a-kiszolgálókat Azure Security Center standard szintű csomagra, akkor nem kell további műveletet végrehajtania. A Azure Security Center automatikusan előkészíti a kiszolgálókat a Windows Defender ATP szolgáltatásba. Ez akár 24 órát is igénybe vehet.

- Ha még soha nem állította be a kiszolgálókat Azure Security Center standard szintű csomagba, a szokásos módon Azure Security Center.

- Ha a-kiszolgálókat a Windows Defender ATP használatával helyezte üzembe:
  - A [kiszolgálói gépek regisztrációjának megszüntetésére szolgáló](https://go.microsoft.com/fwlink/p/?linkid=852906)kapcsolatos útmutatásért tekintse meg a dokumentációt.
  - Ezeket a kiszolgálókat Azure Security Center.

## <a name="access-to-the-windows-defender-atp-portal"></a>Hozzáférés a Windows Defender ATP-portálhoz

Kövesse a [felhasználói hozzáférés kiosztása a portálhoz](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/assign-portal-access-windows-defender-advanced-threat-protection)című témakör utasításait.

## <a name="set-the-firewall-configuration"></a>A tűzfal konfigurációjának beállítása

Ha olyan proxyval vagy tűzfallal rendelkezik, amely blokkolja a névtelen forgalmat, akkor a Windows Defender ATP-érzékelő a rendszerkörnyezetből csatlakozik, győződjön meg arról, hogy a névtelen forgalom engedélyezett. Kövesse a [Windows DEFENDER ATP-szolgáltatáshoz való hozzáférés engedélyezése a proxykiszolgálón](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/configure-proxy-internet-windows-defender-advanced-threat-protection#enable-access-to-microsoft-defender-atp-service-urls-in-the-proxy-server)című témakör utasításait.

## <a name="test-the-feature"></a>A szolgáltatás tesztelése

Jóindulatú Windows Defender ATP-teszt riasztás létrehozása:

1. A Távoli asztal használatával elérheti a Windows Server 2012 R2 vagy a Windows Server 2016 rendszerű virtuális gépeket.  Nyisson meg egy parancssori ablakot.

2. A parancssorba másolja és futtassa a következő parancsot. A parancssori ablak automatikusan bezáródik.

    ```
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-WDATP-test\\invoice.exe'); Start-Process 'C:\\test-WDATP-test\\invoice.exe'
    ```

   ![Egy parancssori ablak a fenti paranccsal](media/security-center-wdatp/image4.jpeg)

3. Ha a parancs sikeres, egy új riasztás jelenik meg a Azure Security Center-irányítópulton és a Windows Defender ATP-portálon. Ez a riasztás néhány percet is igénybe vehet.

4. A Security Center riasztásának áttekintéséhez lépjen a **biztonsági riasztások** >  **gyanús PowerShell parancssori**elemre.

5. A vizsgálat ablakban válassza ki azt a hivatkozást, amely a Windows Defender ATP-portálra mutat.

## <a name="next-steps"></a>További lépések

- [Az Azure Security Center által támogatott platformok és szolgáltatások](security-center-os-coverage.md)
- [Biztonsági szabályzatok beállítása a Azure Security Centerban](tutorial-security-policy.md): Ismerje meg, hogyan konfigurálhatja az Azure-előfizetések és-erőforráscsoportok biztonsági szabályzatait.
- [Biztonsági javaslatok kezelése Azure Security Centerban](security-center-recommendations.md): Ismerje meg, hogy a javaslatok Hogyan segítenek az Azure-erőforrások védelmében.
- [Biztonsági állapot figyelése Azure Security Centerban](security-center-monitoring.md): Az Azure-erőforrások állapotának figyelését ismertető útmutató.
