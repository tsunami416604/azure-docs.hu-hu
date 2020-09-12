---
title: Microsoft Defender komplex veszélyforrások elleni védelem – Azure Security Center
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
ms.date: 04/07/2020
ms.author: memildin
ms.openlocfilehash: e1595d7e205c788f90177836f3c0370681b7747b
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/09/2020
ms.locfileid: "89569084"
---
# <a name="microsoft-defender-advanced-threat-protection-with-azure-security-center"></a>A Microsoft Defender komplex veszélyforrások elleni védelem Azure Security Center

A Azure Security Center a [Microsoft Defender komplex veszélyforrások elleni védelem (ATP)](https://www.microsoft.com/microsoft-365/windows/microsoft-defender-atp) szolgáltatással integrálható az átfogó végpont-észlelési és reagálási (EDR) képességek biztosításához.


## <a name="availability"></a>Rendelkezésre állás

|Szempont|Részletek|
|----|:----|
|Kiadás állapota:|**Általános elérhetőség**|
|Árképzési|Standard csomag|
|Támogatott gépek:|![Yes](./media/icons/yes-icon.png) Windows rendszerű Azure-gépek<br>![Yes](./media/icons/yes-icon.png) Windows rendszerű Azure arc-gépek|
|Szükséges szerepkörök és engedélyek:|Az integráció engedélyezése/letiltása: **biztonsági rendszergazda** vagy **tulajdonos**<br>MDATP-riasztások megtekintése a Security Center-ben: **biztonsági olvasó**, **olvasó**, **erőforráscsoport-közreműködő**, **erőforráscsoport-tulajdonos**, **biztonsági rendszergazda**, **előfizetés tulajdonosa**vagy **előfizetéshez tartozó közreműködő**|
|Felhők|![Yes](./media/icons/yes-icon.png) Kereskedelmi felhők.<br>![No](./media/icons/no-icon.png) A munkaterheléseket futtató GCC-ügyfelek nyilvános Azure-felhőkben<br>![Yes](./media/icons/yes-icon.png) US Gov<br>![No](./media/icons/no-icon.png) Kínai gov, egyéb gov|
|||


## <a name="microsoft-defender-atp-features-in-security-center"></a>A Microsoft Defender ATP funkciói Security Center

A Microsoft Defender ATP a következőket biztosítja:

- **Speciális post-megsértés észlelési érzékelők**: a Microsoft Defender ATP-érzékelők a Windows-kiszolgálókon a viselkedési jelek nagy tömbjét gyűjtik össze.

- **Elemzésen alapuló, felhőalapú post megsértési észlelés**: a Microsoft Defender ATP gyorsan alkalmazkodik a fenyegetések változásához. Fejlett elemzési és big dataeket használ. A Microsoft Defender ATP-t az ismeretlen fenyegetések észlelése érdekében a Intelligens biztonsági gráf hatékonysága fokozza a Windows, az Azure és az Office jelekkel szemben. Kezelhető riasztásokat biztosít, és lehetővé teszi a gyors reagálást.

- **Fenyegetési intelligencia**: a Microsoft Defender ATP riasztásokat állít elő, amikor a támadó eszközöket, technikákat és eljárásokat azonosítja. A Microsoft Threat Hunters és a biztonsági csapatok által létrehozott, a partnerek által biztosított intelligenciával kiegészített adatok használatával működik.


A Defender ATP és a Azure Security Center integrálásával a következő további funkciók is hasznosak lehetnek:

- **Automatizált**előkészítés: az integráció automatikusan engedélyezi a Microsoft Defender ATP-érzékelőt a Azure Security Center által figyelt Windows Server-kiszolgálókon (kivéve, ha Windows Server 2019 rendszert futtatnak).

- **Önálló**üvegtábla: a Azure Security Center-konzol megjeleníti a Microsoft Defender ATP-riasztásokat. További vizsgálathoz használja a Microsoft Defender ATP-t. A Microsoft Defender ATP további információkat tartalmaz, például a riasztási folyamat fáját és az incidens gráfot. Megtekintheti a részletes gépi idővonalat is, amely egy 6 hónapos időszakra visszamenőlegesen mutatja be az összes viselkedést.

    ![Microsoft Defender ATP-oldal részletes információkkal a riasztásról](media/security-center-wdatp/image3.png)

## <a name="platform-support"></a>Platformtámogatás

A Microsoft Defender ATP Security Center támogatja a Windows Server 2016, 2012 R2 és 2008 R2 SP1 észlelését. Az Azure-beli virtuális gépekhez standard szintű előfizetésre és nem Azure-beli virtuális gépekre van szükség, csak a munkaterület szintjén lévő standard csomagra van szükség.

A kiszolgálói végpont monitorozása ezzel az integrációval le lett tiltva az Office 365 GCC-ügyfelek számára.

## <a name="data-storage-location"></a>Adattárolás helye

Ha Azure Security Centert használ a kiszolgálók figyelésére, a rendszer automatikusan létrehoz egy Microsoft Defender ATP-bérlőt. A Microsoft Defender ATP által gyűjtött adatokat a bérlő földrajzi helye tárolja a kiépítés során. Az álneves formában tárolt ügyféladatok a Egyesült Államok központi tároló-és feldolgozási rendszereiben is tárolhatók. 

A konfigurálást követően nem módosíthatja azt a helyet, ahol az adatait tárolja. Ha át kell helyeznie az adatait egy másik helyre, lépjen kapcsolatba Microsoft ügyfélszolgálata a bérlő alaphelyzetbe állításához.


## <a name="onboard-servers-to-security-center"></a>Bevezetési kiszolgálók Security Center 

A kiszolgálók Security Centerbe való bevezetéséhez kattintson az **Azure Security Center ugrás** gombra a Microsoft Defender ATP-kiszolgáló bevezetési kiszolgálóinak bevezetéséhez.

1. A bevezetési **területen válasszon** ki vagy hozzon létre egy munkaterületet az adattároláshoz.

2. Ha nem látja az összes munkaterületet, előfordulhat, hogy az engedélyek hiánya miatt a munkaterület az Azure Security Standard díjszabási szintjére van beállítva. További információ: a [frissítés a Security Center Standard szintjére a fokozott biztonság érdekében](security-center-pricing.md).
    
3. Válassza a **kiszolgálók hozzáadása** lehetőséget a log Analytics ügynök telepítésére vonatkozó utasítások megtekintéséhez. 

4. A bevezetést követően nyomon követheti a gépeket a **számítás és az alkalmazások**területen.

   ![Számítógépek előkészítése](media/security-center-wdatp/onboard-computers.png)

## <a name="enable-microsoft-defender-atp-integration"></a>A Microsoft Defender ATP-integráció engedélyezése

Ha meg szeretné tekinteni, hogy engedélyezve van-e a Microsoft Defender ATP-integrációja, válassza a **Security Center**  >  **díjszabása & beállításokat** > kattintson az előfizetésre.
Itt láthatja az aktuálisan engedélyezett integrációkat.

  ![Azure Security Center veszélyforrások észlelésére vonatkozó beállítások lap, amelyen engedélyezve van a Microsoft Defender ATP-integráció](media/security-center-wdatp/enable-integrations.png)

- Ha már előkészítette a-kiszolgálókat Azure Security Center standard szintű csomagra, akkor nem kell további műveletet végrehajtania. Azure Security Center automatikusan előkészíti a kiszolgálókat a Microsoft Defender ATP szolgáltatásba. A bevezetést akár 24 óráig is eltarthat.

- Ha még soha nem állította be a kiszolgálókat Azure Security Center standard szintű csomagba, a szokásos módon Azure Security Center.

- Ha a kiszolgálókat a Microsoft Defender ATP szolgáltatáson keresztül helyezte üzembe:
  - A [kiszolgálói gépek regisztrációjának megszüntetésére szolgáló](https://go.microsoft.com/fwlink/p/?linkid=852906)kapcsolatos útmutatásért tekintse meg a dokumentációt.
  - Ezeket a kiszolgálókat Azure Security Center.

## <a name="access-to-the-microsoft-defender-atp-portal"></a>Hozzáférés a Microsoft Defender ATP-portálhoz

1. Kövesse a [felhasználói hozzáférés kiosztása a portálhoz](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/assign-portal-access)című témakör utasításait.

1. Győződjön meg arról, hogy rendelkezik-e olyan proxyval vagy tűzfallal, amely blokkolja a névtelen forgalmat. A Defender ATP-érzékelő a rendszerkörnyezetből csatlakozik, így a névtelen forgalomnak engedélyezettnek kell lennie. A Microsoft Defender ATP-portálhoz való akadálymentes hozzáférés biztosításához kövesse a következő témakör utasításait: a [Microsoft DEFENDER ATP szolgáltatáshoz való hozzáférés engedélyezése a proxykiszolgálón](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet#enable-access-to-microsoft-defender-atp-service-urls-in-the-proxy-server).

## <a name="test-the-feature"></a>A szolgáltatás tesztelése

Jóindulatú Microsoft Defender ATP-teszt riasztás létrehozása:

1. Hozzon létre egy "C:\test-MDATP-test" mappát.

1. A Távoli asztal használatával elérheti a Windows Server 2012 R2 vagy a Windows Server 2016 rendszerű virtuális gépeket. Nyisson meg egy parancssori ablakot.

1. A parancssorba másolja és futtassa a következő parancsot. A parancssori ablak automatikusan bezáródik.

    ```
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-MDATP-test\\invoice.exe'); Start-Process 'C:\\test-MDATP-test\\invoice.exe'
    ```

   ![Egy parancssori ablak a fenti paranccsal](media/security-center-wdatp/image4.jpeg)

1. Ha a parancs sikeres, egy új riasztás jelenik meg a Azure Security Center-irányítópulton és a Microsoft Defender ATP-portálon. Ez a riasztás néhány percet is igénybe vehet.

1. A Security Center riasztásának áttekintéséhez lépjen a **biztonsági riasztások**  >  **gyanús PowerShell parancssori**elemre.

1. A vizsgálat ablakban válassza ki a Microsoft Defender ATP-portálra mutató hivatkozást.

## <a name="next-steps"></a>Következő lépések

- [Az Azure Security Center által támogatott platformok és szolgáltatások](security-center-os-coverage.md)
- [Biztonsági szabályzatok beállítása Azure Security Centerban](tutorial-security-policy.md): Ismerje meg, hogyan konfigurálhatja az Azure-előfizetések és-erőforráscsoportok biztonsági szabályzatait.
- [Azure Security Center biztonsági javaslatainak kezelése](security-center-recommendations.md): Ismerje meg, hogyan segítheti az ajánlásokat az Azure-erőforrások védelmében.
- [Biztonsági állapotmonitorozás az Azure Security Centerben](security-center-monitoring.md): Útmutató az Azure-erőforrások állapotának monitorozásához.