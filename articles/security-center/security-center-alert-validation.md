---
title: "Riasztások érvényesítése az Azure Security Centerben | Microsoft Docs"
description: "Ez a dokumentum az Azure Security Center biztonsági riasztásainak érvényesítését ismerteti."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: f8f17a55-e672-4d86-8ba9-6c3ce2e71a57
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2017
ms.author: yurid
ms.translationtype: HT
ms.sourcegitcommit: 12c20264b14a477643a4bbc1469a8d1c0941c6e6
ms.openlocfilehash: d7aa8544f50b42bacfa1e1f16fdce468d8fc81ef
ms.contentlocale: hu-hu
ms.lasthandoff: 09/07/2017

---
# <a name="alerts-validation-in-azure-security-center"></a>Riasztások érvényesítése az Azure Security Centerben
A dokumentum ismerteti, hogyan ellenőrizheti, hogy a rendszere megfelelően konfigurálva van-e az Azure Security Center riasztásaihoz.

## <a name="what-are-security-alerts"></a>Mik azok a biztonsági riasztások?
A Security Center automatikusan gyűjti, elemzi és integrálja az Azure-erőforrások, a hálózat és a csatlakoztatott partnermegoldások, például a tűzfalak és a végpontvédelmi megoldások naplóadatait a fenyegetések észlelése és riasztások küldése érdekében. A biztonsági riasztásokkal kapcsolatos további információkért olvassa el a [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) című cikket, a különböző típusú riasztásokról kapcsolatos további információkért pedig tekintse meg a [Az Azure Security Center biztonsági riasztásainak megismerése](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) című cikket.

## <a name="alert-validation"></a>Riasztások érvényesítése
Miután telepítette a számítógépére a Security Centert, kövesse az alábbi lépéseket azon a számítógépen, ahol Ön szeretne lenni a riasztásban jelentett megtámadott erőforrás:

1. Másoljon egy végrehajtható fájlt (például calc.exe) a számítógép asztalára vagy egy másik tetszőleges könyvtárba.
2. Nevezze át ezt a fájlt **ASC_AlertTest_662jfi039N.exe** névre.
3. Nyissa meg a parancssort, és hajtsa végre a fájlt egy argumentummal (egy hamis argumentumnévvel), például: *ASC_AlertTest_662jfi039N.exe -foo*
4. Várjon 5-10 percet, és nyissa meg a Security Center riasztásait. Ott az alábbihoz hasonló riasztást fog találni:

    ![Riasztások érvényesítése](./media/security-center-alert-validation/security-center-alert-validation-fig2.png)

Amikor ellenőrzi ezt a riasztást, győződjön meg róla, hogy az Argumentumok naplózása beállítás értéke Igaz. Ha hamisként jelenik meg, engedélyezze a parancssori argumentumok naplózását. Ezt a beállítást a következő parancssorral engedélyezheti:

*reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"*


> [!NOTE]
> A funkció bemutatójáért tekintse meg a [Riasztások érvényesítése az Azure Security Centerben](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Alert-Validation-in-Azure-Security-Center) című videót. 

## <a name="see-also"></a>Lásd még:
Ez a cikk a riasztások érvényesítési folyamatát mutatta be. Most, hogy már ismeri az érvényesítést, tekintse meg a következő cikkeket:

* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). A Security Center-riasztások kezelését és a biztonsági eseményekre való válaszadást ismertető útmutató.
* [Biztonsági állapotfigyelés az Azure Security Centerben](security-center-monitoring.md). Az Azure-erőforrások állapotának figyelését ismertető útmutató.
* [Az Azure Security Center biztonsági riasztásainak megismerése](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). A különböző típusú biztonsági riasztásokat ismertető útmutató.
* [Azure Security Center – Hibaelhárítási útmutató](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). A Security Center gyakori problémáinak elhárítását ismereti. 
* [Azure Security Center – gyakori kérdések](security-center-faq.md) Gyakori kérdések a szolgáltatás használatával kapcsolatban.
* [Azure Security blog](http://blogs.msdn.com/b/azuresecurity/) Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.


