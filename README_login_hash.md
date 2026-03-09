
# 🔐 Login forma sa registracijom i heširanjem lozinke

## Cilj zadatka

Potrebno je napraviti **Windows Forms aplikaciju** koja sadrži:

- Login formu
- Registraciju korisnika
- Dugme za prikaz / sakrivanje lozinke
- Čuvanje lozinke kao **heš vrednosti**
- Proveru lozinke prilikom prijave poređenjem heša

Cilj je da učenici nauče kako se u realnim aplikacijama implementira **bezbedna prijava korisnika**.

---

# 1. Osnovna ideja

Aplikacija treba da omogući korisniku da:

- napravi novi nalog putem registracije
- unese korisničko ime i lozinku
- vidi lozinku klikom na dugme ili CheckBox
- prijavi se u sistem
- koristi nalog čija se lozinka **ne čuva kao običan tekst već kao heš**

To znači da se u bazi **nikada ne upisuje prava lozinka**, već samo njen kriptografski sažetak.

---

# 2. Forme u aplikaciji

## 2.1 Login forma

Login forma treba da sadrži sledeće kontrole:

- Label **Korisničko ime**
- TextBox za unos korisničkog imena
- Label **Lozinka**
- TextBox za unos lozinke
- CheckBox ili Button za **prikaz / sakrivanje lozinke**
- Button **Prijava**
- Button **Registracija**
- Label za poruke korisniku

### Funkcionalnost login forme

Korisnik unosi:

- korisničko ime
- lozinku

Klikom na dugme **Prijava** aplikacija:

1. proverava da li su polja popunjena
2. računa heš unesene lozinke
3. traži korisnika u bazi
4. poredi izračunati heš sa hešom iz baze
5. dozvoljava prijavu samo ako se vrednosti poklapaju

---

## 2.2 Forma za registraciju

Forma za registraciju treba da sadrži:

- TextBox za korisničko ime
- TextBox za lozinku
- TextBox za potvrdu lozinke
- CheckBox ili Button za prikaz / sakrivanje lozinke
- Button **Registruj se**
- Button **Nazad**
- poruku o uspehu ili grešci

### Funkcionalnost registracije

Prilikom registracije aplikacija treba da proveri:

- da li su sva polja popunjena
- da li lozinka i potvrda lozinke imaju istu vrednost
- da li korisničko ime već postoji

Ako je sve ispravno:

1. lozinka se **hešira**
2. u bazu se upisuje:
   - korisničko ime
   - heš lozinke
3. korisnik se uspešno registruje

---

# 3. Prikaz i sakrivanje lozinke

Ako je čekirano **Prikaži lozinku**

```
PasswordChar = '\0'
```

Ako nije čekirano

```
PasswordChar = '*'
```

Primer:

```csharp
private void chkPrikaziLozinku_CheckedChanged(object sender, EventArgs e)
{
    if (chkPrikaziLozinku.Checked)
        txtLozinka.PasswordChar = '\0';
    else
        txtLozinka.PasswordChar = '*';
}
```

---

# 4. Čuvanje lozinke kao heš

Sistem **ne sme čuvati lozinku kao običan tekst**.

❌ Pogrešno:

```
marko123
admin
lozinka2025
```

✔ Ispravno:

```
ef92b778bafe771e89245b89ecbc08a44a4e166c06659911881f383d4473e94f
```

---

# 5. Heširanje lozinke

Za zadatak se koristi algoritam:

**SHA‑256**

Proces:

1. korisnik unese lozinku
2. aplikacija izračuna SHA‑256 heš
3. u bazu se čuva samo heš

Originalna lozinka se **nikada ne čuva**.

---

# 6. Čuvanje korisnika u fajlu

Podaci se čuvaju u fajlu:

```
korisnici.txt
```

Format:

```
Username;PasswordHash;Uloga
```

Primer:

```
admin;5e884898da28047151d0e56f8dc6292773603d0d6aabbdd1e6a3d2c4f6b8b123;Administrator
marko;8c6976e5b5410415bde908bd4dee15dfb16f1e4c5f9a7d8c2c8c4a6a5f4c3a2b;Korisnik
ana;2bb80d537b1da3e38bd30361aa855686bde0baaec3e7f9e1f1b4a3c2d1e0f9a8;Gost
```

---

# 7. Čuvanje korisnika (C#)

```csharp
string linija = $"{username};{passwordHash};{uloga}";
File.AppendAllLines("korisnici.txt", new[] { linija });
```

---

# 8. HashHelper klasa

```csharp
using System.Security.Cryptography;
using System.Text;

public static class HashHelper
{
    public static string IzracunajSha256(string tekst)
    {
        using (SHA256 sha256 = SHA256.Create())
        {
            byte[] bytes = Encoding.UTF8.GetBytes(tekst);
            byte[] hash = sha256.ComputeHash(bytes);

            StringBuilder sb = new StringBuilder();

            foreach (byte b in hash)
            {
                sb.Append(b.ToString("x2"));
            }

            return sb.ToString();
        }
    }
}
```

---

# 9. Organizacija projekta

```
SkolskaEvidencija
│
├── Models
│   └── Korisnik.cs
│
├── Repositories
│   ├── IKorisnikRepository.cs
│   └── KorisnikTxtRepository.cs
│
├── Services
│   └── AuthService.cs
│
├── Helpers
│   └── HashHelper.cs
│
├── Forms
│   ├── LoginForm.cs
│   ├── RegisterForm.cs
│
├── Data
│   └── korisnici.txt
│
├── Program.cs
└── App.config
```

---

# 10. Najvažnije pravilo

❗ Lozinka se **nikada ne čuva kao običan tekst**.

Proces:

1. pri registraciji se računa **heš**
2. u bazi se čuva samo **heš**
3. pri loginu se ponovo računa heš i poredi

---

# 11. Cilj zadatka

Učenici kroz ovaj projekat uče:

- kako funkcioniše **login sistem**
- kako se implementira **registracija**
- kako se bezbedno čuva lozinka
- kako funkcioniše **SHA‑256**
- kako sarađuju **forma, service i repository sloj**
