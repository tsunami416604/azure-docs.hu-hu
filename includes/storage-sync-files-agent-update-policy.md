Az Azure File Sync-ügynök rendszeresen frissül majd új funkciók hozzáadása és a talált hibák felszámolása érdekében. Ajánlott engedélyezni a Microsoft Update használatét, hogy a megjelentetésükkor be tudja szerezni az Azure File Sync-ügynök összes frissítését. Azt is megértjük, ha egyes cégek vagy szervezetek szigorúbban kívánják felügyelni és tesztelni a frissítéseket. Az Azure File Sync-ügynök régebbi verziójú példányait használó környezetekben:

- A Storage Sync Service az új főbb verziók első kiadása után három hónapig támogatja az előző főbb verziót. Az 1.\* verziót például a Storage Sync Service a 2.\* verzió kiadása után három hónapig támogatja.
- Három hónap elteltével a Storage Sync Service elkezdi letiltani, hogy a lejárt verziót használó regisztrált kiszolgálók szinkronizálni tudjanak a szinkronizálási csoportjaikkal.
- A három hónap elteltével az előző főbb verzió összes hibajavítása csak az aktuális főbb verzióhoz érhető el.

> [!Note]  
> Bejelentési értesítésen keresztül tudatjuk az Azure Portalon, ha a következő három hónapban lejáró Azure File Sync-verziót használ.