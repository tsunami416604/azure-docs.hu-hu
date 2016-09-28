
<properties
    pageTitle="Az Azure AD Connect Health használata a szinkronizálási szolgáltatással | Microsoft Azure"
    description="Ez az Azure AD Connect Health-oldal bemutatja az Azure AD Connect szinkronizálási szolgáltatás megfigyelését."
    services="active-directory"
    documentationCenter=""
    authors="billmath"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/08/2016"
    ms.author="billmath"/>


# Az Azure AD Connect Health szinkronizálási szolgáltatás használata
Az alábbi dokumentáció az Azure AD Connect (szinkronizálási) szolgáltatás Azure AD Connect Health szolgáltatással végzett megfigyelésére vonatkozik.  Az AD FS az Azure AD Connect Health használatával történő megfigyelésére vonatkozó információkat lásd: [Az Azure AD Connect Health használata az AD FS szolgáltatással](active-directory-aadconnect-health-adfs.md). Az Active Directory tartományi szolgáltatások az Azure AD Connect Health használatával történő megfigyelésével kapcsolatos információkat pedig a [Using Azure AD Connect Health with AD DS](active-directory-aadconnect-health-adds.md) (Az Azure AD Connect Health használata az AD DS szolgáltatással) című témakörben találja.

![Azure AD Connect Health szinkronizálási szolgáltatás](./media/active-directory-aadconnect-health-sync/sync.png)

## Az Azure AD Connect Health szinkronizálási szolgáltatás riasztásai
Az Azure AD Connect Health szinkronizálási szolgáltatás riasztásai szakaszban az aktív riasztások listáját láthatja. Minden egyes riasztás tartalmazza a fontos információkat, a megoldás lépéseit, valamint a kapcsolódó dokumentációra mutató hivatkozásokat. Egy aktív vagy egy megoldott riasztás kiválasztása esetén egy új panel további információkat, a riasztás megoldását segítő lépéseket és további dokumentációkra mutató hivatkozásokat jelenít meg. A már megoldott riasztások esetében az előzményadatokat is megtekintheti.

Egy riasztás kiválasztása esetén további információk, a riasztás megoldását segítő lépések és további dokumentációkra mutató hivatkozások jelennek meg.

![Azure AD Connect szinkronizálási szolgáltatás – hiba](./media/active-directory-aadconnect-health-sync/alert.png)

### Riasztások korlátozott kiértékelése
Ha az Azure AD Connect NEM az alapértelmezett konfigurációt használja (például ha az attribútumszűrés az alapértelmezett konfigurációról egyéni konfigurációra változott), akkor az Azure AD Connect Health-ügynök nem tölti fel az Azure AD Connecttel kapcsolatos hibaeseményeket. 

Ez korlátozza a riasztások kiértékelését a szolgáltatás által. Az Azure Portalban a szolgáltatás alatt ilyenkor megjelenik egy szalagcím, amely ezt az állapotot jelzi.

![Azure AD Connect Health szinkronizálási szolgáltatás](./media/active-directory-aadconnect-health-sync/banner.png)

Ezt úgy módosíthatja, ha a „Beállítások” elemre kattint, és engedélyezi, hogy az Azure AD Connect Health-ügynök minden hibanaplót feltöltsön.

![Azure AD Connect Health szinkronizálási szolgáltatás](./media/active-directory-aadconnect-health-sync/banner2.png)

## Szinkronizálási elemzés
Az Azure AD Connect Health szinkronizálási szolgáltatás legújabb kiadása a következő új képességekkel bővült:

- Szinkronizálási műveletek késleltetése
- Objektummódosítási trend

### Szinkronizálási késések
Ez a funkció egy tendenciagrafikont hoz létre az összekötőkhöz kapcsolódó szinkronizálási műveletek (importálás, exportálás stb.) késéseiről.  Így nemcsak a műveletek késései ismerhetők meg gyorsan és könnyen (hasznos a nagy méretű, tömeges változások esetén), hanem lehetővé teszi a késésekben előforduló, potenciálisan további vizsgálatot igénylő rendellenességek észlelését.

![Szinkronizálási késések](./media/active-directory-aadconnect-health-sync/synclatency.png)

Alapértelmezés szerint csak az Azure AD-összekötő „Exportálás” műveletének késései jelennek meg.  Az összekötő további műveleteinek és más összekötők műveleteinek megtekintéséhez kattintson a jobb egérgombbal a diagramra, és válassza ki az adott műveletet és összekötőt.

### Szinkronizálási objektumok módosításai
Ez a funkció egy tendenciagrafikonon jeleníti meg a kiértékelt és az Azure AD-ba exportált módosítások számát.  Napjainkban az ilyen jellegű információkat nehéz a szinkronizálási naplófájlokból összegyűjteni.  A diagram nemcsak leegyszerűsíti a környezetében bekövetkező változások számának követését, de megjeleníti az előforduló hibákat is.

![Szinkronizálási késések](./media/active-directory-aadconnect-health-sync/syncobjectchanges.png)

## Kapcsolódó hivatkozások

* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Az Azure AD Connect Health-ügynök telepítése](active-directory-aadconnect-health-agent-install.md)
* [Azure AD Connect Health Operations (Az Azure AD Connect Health műveletei)](active-directory-aadconnect-health-operations.md)
* [Az Azure AD Connect Health használata az AD FS szolgáltatással](active-directory-aadconnect-health-adfs.md)
* [Az Azure AD Connect Health használata az AD DS szolgáltatással](active-directory-aadconnect-health-adds.md)
* [Azure AD Connect Health FAQ (Azure AD Connect Health – gyakori kérdések)](active-directory-aadconnect-health-faq.md)
* [Azure AD Connect Health Version History (Az Azure AD Connect Health verzióelőzményei)](active-directory-aadconnect-health-version-history.md)




<!--HONumber=Sep16_HO4-->


