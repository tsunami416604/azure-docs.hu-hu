<properties
    pageTitle="Hibrid identitás: a címtár-integrációs eszközök összehasonlítása | Microsoft Azure"
    description="Ez az oldal egy átfogó táblázatot biztosít, amely összehasonlítja a címtár-integrációhoz használható különböző címtár-integrációs eszközöket."
    services="active-directory"
    documentationCenter=""
    authors="billmath"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/12/2016"
    ms.author="billmath"/>

# Hibrid identitás: a címtár-integrációs eszközök összehasonlítása

Az évek során a címtár-integrációs eszközök bővültek és továbbfejlődtek.  Ez a dokumentum ezen eszközök egyesített nézetét is elérhetővé teszi, valamint az egyes eszközökben elérhető funkciók összehasonlíthatóságát biztosítja.

<!-- The hardcoded link is a workaround for campaign ids not working in acom links-->

>[AZURE.NOTE] Az Azure AD Connect magában foglalja a korábban Dirsync és AAD Sync néven kiadott összetevőket és funkciókat. Ezek az eszközök már nem érhetők el különállóan, és minden jövőbeli fejlesztés az Azure AD Connect frissítéseiben fog szerepelni, hogy mindig tudja, honnan szerezheti be a legújabb funkciókat.
>
>A DirSync és az Azure AD Sync elavultnak számít. További információ: [A Windows Azure Active Directory Sync („DirSync”) és az Azure Active Directory Sync („Azure AD Sync”) frissítése](https://azure.microsoft.com/documentation/articles/active-directory-aadconnect-dirsync-deprecated/?WT.mc_id=DirSyncDepACOM).


A következő magyarázatot használhatja mindegyik táblázathoz.

● = Már elérhető  
JK = Jövőbeli kiadás  
NyE = Nyilvános előzetes verzió  

## Helyszínről felhőbe végzett szinkronizálás

| Funkció  | Azure Active Directory Connect  | Azure Active Directory Synchronization Services (AAD Sync) | Azure Active Directory Synchronization Tool (DirSync)| Forefront Identity Manager 2010 R2 (FIM) |Microsoft Identity Manager 2016 (MIM)|
| :-------- |:--------:|:--------:|:--------:|:--------:|:--------:
| Csatlakozás egyetlen helyszíni AD-erdőhöz | ● | ● | ● | ● |● |
| Csatlakozás több helyszíni AD-erdőhöz |●  | ● |  | ● |● |
| Csatlakozás több helyszíni Exchange-szervezethez | ● |  |  |  | |
| Csatlakozás egyetlen helyszíni LDAP-címtárhoz | JK |  |  | ● |● |
| Csatlakozás több helyszíni LDAP-címtárakhoz |JK  |  |  | ● |● |
| Csatlakozás helyszíni AD-hez és helyszíni LDAP-címtárakhoz |JK  |  |  | ● |● |
| Csatlakozás egyedi rendszerekhez (SQL, Oracle, MySQL stb.) | JK |  |  | ● |● |
| Ügyfél által meghatározott attribútumok szinkronizálása (címtárbővítmények) | ● |  |  |  |  |
|Csatlakozás helyszíni HR-hez (SAP, Oracle eBusiness, PeopleSoft)| JK |  |  | ● |● |
|Támogatja a FIM-szinkronizálási szabályokat és összekötőket a helyszíni rendszerekre történő üzembe helyezéshez.|  |  |  | ● |● |

## Felhőről helyszínre végzett szinkronizálás

| Funkció  | Azure Active Directory Connect  | Azure Active Directory Synchronization Services | Azure Active Directory Synchronization Tool (DirSync) | Forefront Identity Manager 2010 R2 (FIM) |Microsoft Identity Manager 2016 (MIM)|
| :-------- |:--------:|:--------:|:--------:|:--------:|:--------:
| Eszközök visszaírása | ● |  | ● |  ||
| Attribútum visszaírása (hibrid Exchange-környezethez) | ● | ● | ● | ● |● |
| Felhasználók és csoportobjektumok visszaírása |  ●|  | |  ||
| Jelszavak visszaírása (önkiszolgáló jelszó-visszaállításból (SSPR) és jelszómódosításból) |  ● | ● |  |  ||



## Hitelesítési funkciók támogatása

| Funkció  | Azure Active Directory Connect | Azure Active Directory Synchronization Services | Azure Active Directory Synchronization Tool (DirSync) | Forefront Identity Manager 2010 R2 (FIM) |Microsoft Identity Manager 2016 (MIM)|
| :-------- |:--------:|:--------:|:--------:|:--------:|:--------:
| Jelszó-szinkronizálás egyetlen helyszíni AD-erdőhöz | ● | ● | ● |  ||
| Jelszó-szinkronizálás több helyszíni AD-erdőhöz |  ●| ● |  |  ||
| Egyszeri bejelentkezés összevonással | ● | ● | ● | ● |● |
| Jelszavak visszaírása (SSPR-ből és jelszómódosításból) |●  | ● |  |  ||



## Beállítás és telepítés

| Funkció  | Azure Active Directory Connect  | Azure Active Directory Synchronization Services | Azure Active Directory Synchronization Tool (DirSync) | Microsoft Identity Manager 2016 (MIM) |
| :-------- |:--------:|:--------:|:--------:|:--------:
| Támogatja a tartományvezérlőre történő telepítést | ● | ● | ● |  |
| Támogatja az SQL Express használatát | ● | ● | ● |  |
| Egyszerű frissítés DirSync-ről |● |  |  |  |
| Rendszergazdai felület honosítása Windows Server-nyelvekre | ● | ● | ● |  |
|Végfelhasználói felület honosítása Windows Server-nyelvekre| |  |  |● |
| A Windows Server 2008 és a Windows Server 2008 R2 támogatása | ● Szinkronizáláshoz, nem összevonáshoz| ● | ●  | ● |
| A Windows Server 2012 és a Windows Server 2012 R2 támogatása | ● | ● | ● | ● |

## Szűrés és konfigurálás

Funkció  | Azure Active Directory Connect | Azure Active Directory Synchronization Services | Azure Active Directory Synchronization Tool (DirSync) | Forefront Identity Manager 2010 R2 (FIM)| Microsoft Identity Manager 2016 (MIM)
:-------- |:--------:|:--------:|:--------:|:--------:|:--------:|
Szűrés a tartományokon és szervezeti egységeken | ● | ● | ● | ●  | ●
Szűrés az objektumok attribútumértékein | ● | ● | ● | ●| ●
A minimális attribútumkészletek szinkronizálásának engedélyezése (MinSync) | ● | ● |  ||
Különböző szolgáltatássablonok alkalmazásának engedélyezése az attribútumfolyamokhoz |●  | ● |  ||
A folyamból az attribútumok eltávolításának engedélyezése az AD-ből az Azure AD-ba | ● | ● |  |  |
Az attribútumfolyamok speciális testreszabásának engedélyezése | ● | ● |  | ●  | ●

## További lépések
További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md).



<!--HONumber=Jun16_HO2-->


