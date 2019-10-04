---
title: A Microsoft Defender komplex veszélyforrások elleni védelem Azure Security Center
description: Ez a dokumentum bemutatja a Azure Security Center és a Microsoft Defender komplex veszélyforrások elleni védelemének integrációját.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/24/2019
ms.author: memildin
ms.openlocfilehash: 07934e3c7d9f61ecb4153e6a169882509a81dc75
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2019
ms.locfileid: "71241040"
---
# <a name="microsoft-defender-advanced-threat-protection-with-azure-security-center"></a>A Microsoft Defender komplex veszélyforrások elleni védelem Azure Security Center

A Azure Security Center a [Microsoft Defender komplex veszélyforrások elleni védelem](https://www.microsoft.com/microsoft-365/windows/microsoft-defender-atp) (ATP) integrálásával kiterjeszti a felhőalapú számítási feladatok védelmi platformokra vonatkozó ajánlatait.
Ez a változás átfogó végpontészlelési és -válaszolási (EDR) képességeket tesz lehetővé. A Microsoft Defender ATP-integrációval szokatlanokat lehet kimutatni. A Azure Security Center által figyelt kiszolgálói végpontokon a speciális támadások észlelésére és reagálására is lehetőség van.

## <a name="microsoft-defender-atp-features-in-security-center"></a>A Microsoft Defender ATP funkciói Security Center

A Microsoft Defender ATP használatakor a következőket kapja:

- **Speciális, a szabálysértés utáni észlelési érzékelők**: A Windows Server rendszerhez készült Microsoft Defender ATP-érzékelők a viselkedési jelek nagy tömbjét gyűjtik össze.

- **Elemzésen alapuló, felhőalapú utólagos szabálysértés észlelése**: A Microsoft Defender ATP gyorsan alkalmazkodik a fenyegetések változásához. Fejlett elemzési és big dataeket használ. A Microsoft Defender ATP-t az ismeretlen fenyegetések észlelése érdekében a Intelligens biztonsági gráf hatékonysága fokozza a Windows, az Azure és az Office jelekkel szemben. Kezelhető riasztásokat biztosít, és lehetővé teszi a gyors reagálást.

- **Fenyegetés intelligencia**: A Microsoft Defender ATP riasztásokat állít elő, amikor a támadó eszközöket, technikákat és eljárásokat azonosítja. A Microsoft Threat Hunters és a biztonsági csapatok által létrehozott, a partnerek által biztosított intelligenciával kiegészített adatok használatával működik.

A következő képességek érhetők el a Azure Security Centerban:

- **Automatizált**előkészítés: A Microsoft Defender ATP-érzékelő automatikusan engedélyezve van a Azure Security Centerba beépített Windows-kiszolgálókon.

- **Önálló**üvegtábla: A Azure Security Center-konzolon a Microsoft Defender ATP-riasztások jelennek meg.

- **Részletes gépi vizsgálat**: Azure Security Center ügyfeleink a Microsoft Defender ATP-konzollal részletes vizsgálatot végezhetnek a szerződésszegés hatókörének felderítése érdekében.

![Azure Security Center a riasztások listájának megjelenítése és az egyes riasztások általános információi](media/security-center-wdatp/image1.png)

További vizsgálathoz használja a Microsoft Defender ATP-t. A Microsoft Defender ATP további információkat tartalmaz, például a riasztási folyamat fáját és az incidens gráfot. Megtekintheti a részletes gépi idővonalat is, amely egy 6 hónapos időszakra visszamenőlegesen mutatja be az összes viselkedést.

![Microsoft Defender ATP-oldal részletes információkkal a riasztásról](media/security-center-wdatp/image3.png)

## <a name="platform-support"></a>Platformtámogatás

A Microsoft Defender ATP a Security Center támogatja a Windows Server 2016, 2012 R2 és 2008 R2 SP1 operációs rendszerek észlelését a standard szintű szolgáltatás előfizetésében.

> [!NOTE]
> Ha Azure Security Centert használ a kiszolgálók figyelésére, a rendszer automatikusan létrehoz egy Microsoft Defender ATP-bérlőt, és a Microsoft Defender ATP-adatvédelmet alapértelmezés szerint Európában tárolja. Ha át kell helyeznie az adatait egy másik helyre, kapcsolatba kell lépnie Microsoft ügyfélszolgálata a bérlő alaphelyzetbe állításához. Az integrációt használó kiszolgálói végpont figyelése le van tiltva az Office 365 GCC-ügyfelek számára.

## <a name="onboarding-servers-to-security-center"></a>Kiszolgálók bevezetése Security Center 

A kiszolgálók Security Centerbe való bevezetéséhez kattintson az **Azure Security Center ugrás** gombra a Microsoft Defender ATP-kiszolgáló bevezetési kiszolgálóinak bevezetéséhez.

1. A bevezetési **területen válasszon** ki vagy hozzon létre egy munkaterületet az adattároláshoz. <br>
2. Ha nem látja az összes munkaterületet, akkor az engedélyek hiánya miatt előfordulhat, hogy a munkaterület az Azure Security Standard csomagra van beállítva. További információ: a [frissítés a Security Center Standard szintjére a fokozott biztonság érdekében](security-center-pricing.md).
    
3. Válassza a **kiszolgálók hozzáadása** lehetőséget a Microsoft monitoring Agent telepítésére vonatkozó utasítások megtekintéséhez. 

4. A bevezetést követően nyomon követheti a gépeket a **számítás és az alkalmazások**területen.

   ![Számítógépek előkészítése](media/security-center-wdatp/onboard-computers.png)

## <a name="enable-microsoft-defender-atp-integration"></a>A Microsoft Defender ATP-integráció engedélyezése

Ha meg szeretné tekinteni, hogy engedélyezve van-e a Microsoft Defender ATP-integrációja, válassza a **Security Center** > **díjszabása & beállításokat** > kattintson az előfizetésre.
Itt láthatja az aktuálisan engedélyezett integrációkat.

  ![Azure Security Center veszélyforrások észlelésére vonatkozó beállítások lap, amelyen engedélyezve van a Microsoft Defender ATP-integráció](media/security-center-wdatp/enable-integrations.png)

- Ha már előkészítette a-kiszolgálókat Azure Security Center standard szintű csomagra, akkor nem kell további műveletet végrehajtania. Azure Security Center automatikusan előkészíti a kiszolgálókat a Microsoft Defender ATP szolgáltatásba. A bevezetést akár 24 óráig is eltarthat.

- Ha még soha nem állította be a kiszolgálókat Azure Security Center standard szintű csomagba, a szokásos módon Azure Security Center.

- Ha a kiszolgálókat a Microsoft Defender ATP szolgáltatáson keresztül helyezte üzembe:
  - A [kiszolgálói gépek regisztrációjának megszüntetésére szolgáló](https://go.microsoft.com/fwlink/p/?linkid=852906)kapcsolatos útmutatásért tekintse meg a dokumentációt.
  - Ezeket a kiszolgálókat Azure Security Center.

## <a name="access-to-the-microsoft-defender-atp-portal"></a>Hozzáférés a Microsoft Defender ATP-portálhoz

Kövesse a [felhasználói hozzáférés kiosztása a portálhoz](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/assign-portal-access)című témakör utasításait.

## <a name="set-the-firewall-configuration"></a>A tűzfal konfigurációjának beállítása

Ha olyan proxyval vagy tűzfallal rendelkezik, amely blokkolja a névtelen forgalmat, mivel a Microsoft Defender ATP-érzékelő a rendszerkörnyezetből csatlakozik, győződjön meg arról, hogy a névtelen forgalom engedélyezett. Kövesse a következő témakör utasításait: a [Microsoft DEFENDER ATP-szolgáltatáshoz való hozzáférés engedélyezése a proxykiszolgálón](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet#enable-access-to-microsoft-defender-atp-service-urls-in-the-proxy-server).

## <a name="test-the-feature"></a>A szolgáltatás tesztelése

Jóindulatú Microsoft Defender ATP-teszt riasztás létrehozása:

1. A Távoli asztal használatával elérheti a Windows Server 2012 R2 vagy a Windows Server 2016 rendszerű virtuális gépeket.  Nyisson meg egy parancssori ablakot.

2. A parancssorba másolja és futtassa a következő parancsot. A parancssori ablak automatikusan bezáródik.

    ```
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-WDATP-test\\invoice.exe'); Start-Process 'C:\\test-WDATP-test\\invoice.exe'
    ```

   ![Egy parancssori ablak a fenti paranccsal](media/security-center-wdatp/image4.jpeg)

3. Ha a parancs sikeres, egy új riasztás jelenik meg a Azure Security Center-irányítópulton és a Microsoft Defender ATP-portálon. Ez a riasztás néhány percet is igénybe vehet.

4. A Security Center riasztásának áttekintéséhez lépjen a **biztonsági riasztások** >  **gyanús PowerShell parancssori**elemre.

5. A vizsgálat ablakban válassza ki a Microsoft Defender ATP-portálra mutató hivatkozást.

## <a name="next-steps"></a>További lépések

- [Az Azure Security Center által támogatott platformok és szolgáltatások](security-center-os-coverage.md)
- [Biztonsági szabályzatok beállítása a Azure Security Centerban](tutorial-security-policy.md): Ismerje meg, hogyan konfigurálhatja az Azure-előfizetések és-erőforráscsoportok biztonsági szabályzatait.
- [Biztonsági javaslatok kezelése Azure Security Centerban](security-center-recommendations.md): Ismerje meg, hogy a javaslatok Hogyan segítenek az Azure-erőforrások védelmében.
- [Biztonsági állapot figyelése Azure Security Centerban](security-center-monitoring.md): Az Azure-erőforrások állapotának figyelését ismertető útmutató.
