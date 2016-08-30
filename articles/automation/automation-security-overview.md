<properties
   pageTitle="Az Azure Automation biztonsága"
   description="Ez a cikk az Automation biztonságának és az Azure Automation-fiókok számára elérhető különböző hitelesítési módszerek áttekintését nyújtja."
   services="automation"
   documentationCenter=""
   authors="MGoedtel"
   manager="jwhit"
   editor="tysonn"
   keywords="automation security, secure automation" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/10/2016"
   ms.author="magoedte" />

# Az Azure Automation biztonsága
Az Azure Automation lehetővé teszi a feladatok automatizálását az Azure erőforrásain, továbbá olyan felhőszolgáltatókkal, mint az Amazon webszolgáltatások (AWS).  Annak érdekében, hogy a forgatókönyv elvégezze a szükséges műveleteket, engedélyekkel kell rendelkeznie az erőforrások biztonságos eléréséhez az előfizetésben szükséges minimális jogokkal.  
Ez a cikk lefedi az Azure Automation által támogatott különböző hitelesítési forgatókönyveket, és megmutatja, hogyan kell megtenni az első lépéseket a kezelendő környezettől vagy környezetektől függően.  

## Az Automation-fiókok áttekintése
Amikor először indítja el az Azure Automationt, legalább egy Automation-fiókot létre kell hoznia. Az Automation-fiókok lehetővé teszik, hogy elkülönítse az Automation erőforrásait (forgatókönyvek, adategységek, konfigurációk) a más Automation-fiókokban tárolt erőforrásoktól. Az Automation-fiókok segítségével külön logikai környezetekre választhatja szét az erőforrásokat. Használhat például egy fiókot fejlesztéshez, egy másikat az üzemi használatra, egy harmadikat pedig a helyszíni környezethez.  Az Azure Automation-fiók különbözik a Microsoft-fiókjától vagy az Azure-előfizetésében létrehozott fiókoktól.

Az Azure-fiókokhoz tartozó Automation-erőforrások egy Azure-régióhoz tartoznak, de az Automation-fiókok képesek bármelyik régióban kezelni az erőforrásokat. A különböző régiókban levő Automation-fiókok létrehozásának fő oka az lehet, ha a házirendeknek adatokra és erőforrásokra van szükségük ahhoz, hogy külön régióra különülhessenek el.

>[AZURE.NOTE]Az Azure portálon létrehozott Automation-fiókok, valamint a rajtuk tárolt erőforrások nem érhetők el a klasszikus Azure portálról. Ha ezeket fiókokat vagy az erőforrásaikat Windows PowerShellel felügyeli, az Azure Resource Manager modulokat kell használnia.

Az összes feladatot, amelyet az Azure Resource Manager (ARM) és az Azure Automation parancsmagjainak használatával az erőforrásokon végrehajt, hitelesíteni kell az Azure Active Directory szervezetiidentitás-hitelesítésével.  A tanúsítványalapú hitelesítés volt az eredeti hitelesítési módszer az Azure szolgáltatásfelügyelet (ASM) módban, de azt bonyolult volt beállítani.  Az Azure felé egy Azure AD-felhasználóval történő hitelesítés lehetőségét 2014-ben vezettük be, nem csak a hitelesítési fiókok konfigurálási folyamatának leegyszerűsítéséért, hanem hogy az Azure-ba nem interaktívan, egyetlen felhasználói fiókkal történő hitelesítés képességét is támogassa, amely működött az ASM és ARM módokkal is.   

Nemrég kiadtunk egy másik frissítést, amellyel már automatikusan hozunk létre egy Azure AD egyszerű szolgáltatási objektumot, amikor az Automation-fiók létrejön. Ezt Azure-beli futtató fióknak hívják, és ez az Azure Resource Manager forgatókönyv-automatizálásának alapértelmezett hitelesítési módszere.     

A szerepköralapú hozzáférés-vezérlés elérhető az ARM-módban, hogy hozzáférést adjon az engedélyezett műveleteknek egy Azure AD-felhasználói fiókhoz és egyszerű szolgáltatáshoz, és hitelesítse ez utóbbit.  Az Automation-engedélyek kezelésére használt modell fejlesztéséhez további információkért olvassa el [Az Azure Automation szerepköralapú hozzáférés-vezérlése](../automation/automation-role-based-access-control.md) című cikket.  

Az adatközpontban egy hibrid forgatókönyv-feldolgozón vagy az AWS számítástechnikai szolgáltatásain futó forgatókönyvek nem használhatják ugyanazt a módszert, amelyet az Azure-erőforrásokon hitelesítő forgatókönyvek használnak.  Ennek oka az, hogy azok az erőforrások az Azure-on kívül futnak, és emiatt az Automation szolgáltatásban meghatározott saját biztonsági hitelesítő adataikra lesz szükség a helyileg elérhető erőforrásokhoz történő hitelesítéshez.  

## Hitelesítési módszerek

A következő táblázat összefoglalja az Azure Automation által támogatott összes környezet különböző hitelesítési módszereit, valamint az azt ismertető cikket, hogy miként lehet beállítani a hitelesítést a forgatókönyvekhez.

Módszer  |  Környezet  | Cikk
----------|----------|----------
Azure AD felhasználói fiók | Azure Resource Manager és Azure szolgáltatásfelügyelet | [Forgatókönyvek hitelesítése Azure AD-felhasználói fiókkal](../automation/automation-sec-configure-aduser-account.md)
Azure AD egyszerű szolgáltatási objektum | Azure Resource Manager | [Forgatókönyvek hitelesítése Azure-beli futtató fiókkal](../automation/automation-sec-configure-azure-runas-account.md)
Windows-hitelesítés | Helyszíni adatközpont | [Forgatókönyvek hitelesítése hibrid forgatókönyv-feldolgozókhoz](../automation/automation-hybrid-runbook-worker.md)
AWS hitelesítő adatok | Amazon webszolgáltatások | [Forgatókönyvek hitelesítése az Amazon webszolgáltatásokkal (AWS)](../automation/automation-sec-configure-aws-account.md)



<!--HONumber=jun16_HO2-->


