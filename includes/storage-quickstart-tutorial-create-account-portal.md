## <a name="create-a-storage-account-using-the-azure-portal"></a>Tárfiók létrehozása az Azure Portal használatával

Először hozzon létre egy általános célú tárfiókot ehhez a gyors útmutatóhoz. 

1. Lépjen az [Azure Portalra](https://portal.azure.com) és jelentkezzen be az Azure-fiókjával. 
2. A Központ menüben válassza az **Új** > **Tárolás** > **Tárfiók – blob, fájl, tábla, üzenetsor** elemet. 
3. Adja meg a tárfiók egyedi nevét. Ne feledje ezeket a szabályokat a tárfiók elnevezésekor:
    - A név csak 3 és 24 karakter közötti hosszúságú lehet.
    - A név csak számokból és kisbetűkből állhat.
4. Ellenőrizze, hogy a következő alapértelmezett értékek be vannak-e állítva: 
    - Az **Üzemi modell** értéke **Resource Manager**.
    - A **Fióktípus** értéke **Általános célú**.
    - A **Teljesítmény** értéke **Standard**.
    - A **Replikáció** értéke **Helyileg redundáns tárolás (LRS)**.
5. Válassza ki előfizetését. 
6. Hozzon létre egy új **Erőforráscsoportot**, és adjon meg hozzá egy egyedi nevet. 
7. Válassza ki a tárfiókhoz használni kívánt **Helyet**.
8. Jelölje be a **Rögzítés az irányítópulton** jelölőnégyzetet, majd kattintson a **Létrehozás** gombra a tárfiók létrehozásához. 

A tárfiók létrehozása után az rögzítve lesz az irányítópulton. Kattintson rá a megnyitásához. A **Beállítások** oldalon kattintson a **Hozzáférési kulcs** gombra. Jelölje ki az elsődleges kulcsot, és másolja a társított **Kapcsolati karakterláncot** a vágólapra, majd illessze be azt egy szövegszerkesztőbe, hogy később használhassa.
