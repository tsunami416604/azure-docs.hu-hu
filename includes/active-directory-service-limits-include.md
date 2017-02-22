Az alábbiakban az Azure Active Directory szolgáltatás használati és egyéb szolgáltatási korlátai olvashatóak.

| Kategória | Korlátok |
| --- | --- |
| Könyvtárak |Egyetlen felhasználóhoz legfeljebb 20 Azure Active Directory-címtár tartozhat.<br />Példák a lehetséges kombinációkra: <ul> <li>Egy felhasználó létrehoz 20 címtárat.</li><li>Egy felhasználót hozzáadnak 20 címtárhoz tagként.</li><li>Egy felhasználó létrehoz 10 címtárat, később pedig mások további 10 különböző címtárhoz adják hozzá tagként.</li></ul> |
| Objektumok |<ul><li>Az Azure Active Directory ingyenes kiadásának felhasználói egyetlen címtárban legfeljebb 500 000 objektumot használhatnak.</li><li>A nem rendszergazdai jogosultságú felhasználók legfeljebb 250 objektumot hozhatnak létre.</li></ul> |
| Sémabővítmények |<ul><li>A karakterlánc típusú bővítmények legfeljebb 256 karakterből állhatnak. </li><li>A bináris típusú bővítmények legfeljebb 256 bájtosak lehetnek.</li><li>Egyetlen objektumhoz legfeljebb&100; bővítményértéket lehet írni (az ÖSSZES típust és az ÖSSZES alkalmazást beleértve).</li><li>Csak a „User”, „Group”, „TenantDetail”, „Device”, „Application” és „ServicePrincipal” entitások bővíthetőek „String” típusú vagy „Binary” típusú egyértékű attribútumokkal.</li><li>A sémabővítmények csak a Graph API-version 1.21-preview esetében érhetőek el. Az alkalmazás számára írási hozzáférést kell biztosítani a bővítmények regisztrálásához.</li></ul> |
| Alkalmazások |Egyetlen alkalmazásnak legfeljebb 10 felhasználó lehet a tulajdonosa. |
| Csoportok |<ul><li>Egyetlen csoportnak legfeljebb 10 felhasználó lehet a tulajdonosa.</li><li>Az Azure Active Directoryban egyetlen csoportnak tetszőleges számú objektum lehet a tagja.</li><li>Az Azure Active Directory címtár-szinkronizálással (DirSync) egy csoportnak legfeljebb 15 000 tagja szinkronizálható a helyszíni Active Directoryból az Azure Active Directoryba.</li><li>Az Azure AD Connecttel egy csoportnak legfeljebb 50 000 tagja szinkronizálható a helyszíni Active Directoryból az Azure Active Directoryba.</li></ul> |
| Hozzáférési panel |<ul><li>A Prémium szintű Azure AD vagy Nagyvállalati mobilitási csomag licenccel rendelkező végfelhasználók számára a hozzáférési panelen megjelenő alkalmazások számának nincs korlátja.</li><li>Az Azure Active Directory ingyenes vagy alapszintű kiadásához tartozó licenccel rendelkező végfelhasználók számára a hozzáférési panelen legfeljebb 10 alkalmazáscsempe látható (például Box, Salesforce vagy Dropbox). A korlátozás a rendszergazdai fiókokra nem vonatkozik.</li></ul> |
| Jelentések | A jelentésekben legfeljebb 1000 sort lehet megtekinteni vagy letölteni. A további adatokat a rendszer csonkolja. |
| Felügyeleti egységek | Egy objektum legfeljebb 30 felügyeleti egységnek lehet a tagja. |


<!--HONumber=Feb17_HO2-->


