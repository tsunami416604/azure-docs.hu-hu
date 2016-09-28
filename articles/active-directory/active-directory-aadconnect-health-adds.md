
<properties
    pageTitle="Az Azure AD Connect Health használata az AD DS szolgáltatással | Microsoft Azure"
    description="Ez az Azure AD Connect Health-oldal ismerteti az AD DS figyelésének módját."
    services="active-directory"
    documentationCenter=""
    authors="arluca"
    manager="samueld"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/14/2016"
    ms.author="arluca"/>


# Az Azure AD Connect Health használata az AD DS szolgáltatással
Az alábbi dokumentáció az Active Directory tartományi szolgáltatások Azure AD Connect Health használatával történő figyelésére vonatkozik. Ez kiterjed a Windows Server 2008 R2, Windows Server 2012 vagy Windows Server 2012 R2 rendszerre telepített AD DS-re is.

Az AD FS az Azure AD Connect Health használatával történő megfigyelésére vonatkozó információkat lásd: [Az Azure AD Connect Health használata az AD FS szolgáltatással](active-directory-aadconnect-health-adfs.md). Az Azure AD Connect (szinkronizálási szolgáltatás) az Azure AD Connect Health használatával történő megfigyelésével kapcsolatos információkat [Az Azure AD Connect Health szinkronizálási szolgáltatás használata](active-directory-aadconnect-health-sync.md) című témakörben tekintheti meg.

![Azure AD Connect Health for AD DS](./media/active-directory-aadconnect-health/aadconnect-health-adds-entry.png)

## Az Azure AD Connect Health for AD DS riasztásai
A Riasztások szakasz az Azure AD Connect Health for AD DS-en belül a tartományvezérlőkhöz kapcsolódó aktív és megoldott riasztásokról nyújt listát. Egy aktív vagy egy megoldott riasztás kiválasztása egy új, további információkat tartalmazó panelt nyit meg, amely a megoldás lépései mellett támogatási dokumentumokra mutató hivatkozásokat jelenít meg. Minden riasztástípusnak egy vagy több példánya lehet, amelyek megfelelnek a meghatározott riasztás által érintett tartományvezérlőknek. A riasztási panel alsó részéhez közel kiválaszthatja az érintett tartományvezérlőket, majd egy új panel nyílik meg, további részletekkel a meghatározott riasztási eseményről.

Az e-mailes értesítések engedélyezése a riasztásokhoz szintén ezen a panelen érhető el, csakúgy, mint a megjelenített időtartomány megváltoztatása. Az időtartomány kiterjesztése lehetővé teszi korábbi, megoldott riasztások megjelenítését.

![Azure AD Connect szinkronizálási szolgáltatás – hiba](./media/active-directory-aadconnect-health/aadconnect-health-adds-alerts.png)

## Tartományvezérlők
Ez az irányítópult a környezet topológiai áttekintését teszi lehetővé, a főbb üzemeltetési mérőszámokkal és minden megfigyelt tartományvezérlő állapotadataival. A megjelenített mérőszámok segítenek a további vizsgálatot igénylő tartományvezérlők gyors beazonosításában. Alapértelmezés szerint csak az oszlopok egy része jelenik meg, de az oszlopok parancsra kattintva az elérhető teljes oszlopkészletet megjelenítheti. A figyelmet igénylő oszlopok kiválasztásával ezt az irányítópultot egy könnyen használható hellyé alakítja, ahol megtekintheti az AD DS-környezet állapotát. 

![Tartományvezérlők](./media/active-directory-aadconnect-health/aadconnect-health-adds-domainsandsites-dashboard.png)

A tartományvezérlők csoportosíthatóak a hozzájuk tartozó tartomány vagy hely alapján, ami hasznos lehet a környezeti topológia megértésében. Végül, ha duplán kattint a panel fejlécére, az irányítópult teljes méretben jelenik meg a képernyőterület maximális kihasználásának érdekében. Ez akkor lehet hasznos, ha több oszlopot jelenít meg egyszerre. 

## Replikáció állapota
Ez az irányítópult a megfigyelt tartományvezérlők replikációs állapotának és a replikáció topológiájának a megtekintését teszi lehetővé. Megjelenik a legutóbbi replikációs kísérlet állapota, a bármely felmerülő hibával kapcsolatos segítséget tartalmazó dokumentációval együtt. Egy hibával rendelkező tartományvezérlő kiválasztása egy új, további információkat tartalmazó panelt nyit meg, amely a megoldás lépései mellett hibaelhárítási dokumentációkra mutató hivatkozásokat jelenít meg. 

![Replikáció állapota](./media/active-directory-aadconnect-health/aadconnect-health-adds-replication.png)

## Figyelés
Ez a funkció különböző teljesítményszámlálók grafikus trendjeit jeleníti meg, amelyeket a rendszer folyamatosan gyűjt az egyes megfigyelt tartományvezérlőkről. Egy tartományvezérlő teljesítménye könnyen összehasonlítható más megfigyelt tartományvezérlőkkel az erdőben. Ezenfelül különböző teljesítményszámlálókat láthat egymás mellett, amely a környezetében történő hibaelhárítás során lehet hasznos. 

![Figyelés](./media/active-directory-aadconnect-health/aadconnect-health-adds-monitoring.png)

Alapértelmezés szerint négy teljesítményszámláló van kiválasztva, de belefoglalhat továbbiakat is úgy, hogy a szűrő parancsra kattint, és kijelöli a kívánt teljesítményszámlálót, vagy törli annak jelölését. Továbbá, ha egy adott teljesítményszámláló diagramjára kattint, egy új panel fog megnyílni, amely tartalmazza a megfelelő adatpontokat mindegyik megfigyelt tartományvezérlőhöz.

## Kapcsolódó hivatkozások

* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Az Azure AD Connect Health-ügynök telepítése](active-directory-aadconnect-health-agent-install.md)
* [Azure AD Connect Health Operations (Az Azure AD Connect Health műveletei)](active-directory-aadconnect-health-operations.md)
* [Az Azure AD Connect Health használata az AD FS szolgáltatással](active-directory-aadconnect-health-adfs.md)
* [Az Azure AD Connect Health szinkronizálási szolgáltatás használata](active-directory-aadconnect-health-sync.md)
* [Azure AD Connect Health FAQ (Azure AD Connect Health – gyakori kérdések)](active-directory-aadconnect-health-faq.md)
* [Azure AD Connect Health Version History (Az Azure AD Connect Health verzióelőzményei)](active-directory-aadconnect-health-version-history.md)



<!--HONumber=Sep16_HO4-->


