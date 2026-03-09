# 🔐 SISTEM KORISNIKA I DOZVOLA

## 🎯 Cilj zadatka

Cilj ovog zadatka je da učenici kroz praktičan projekat primene sledeće koncepte objektno-orijentisanog programiranja:

- apstraktne klase  
- nasleđivanje  
- interfejse  
- enumeracije (enum)  
- polimorfizam  

U projektu će se implementirati **sistem korisnika sa različitim dozvolama u Windows Forms aplikaciji**.

---

# 1️⃣ Osnovna ideja sistema

U sistemu postoje različite vrste korisnika:

- Gost
- Korisnik sistema
- Administrator

Svi oni su korisnici, ali imaju **različite dozvole u aplikaciji**.

| Akcija | Gost | Korisnik | Administrator |
|------|------|------|------|
| Pregled podataka | ✔ | ✔ | ✔ |
| Dodavanje podataka | ✖ | ✔ | ✔ |
| Izmena podataka | ✖ | ✔ | ✔ |
| Brisanje podataka | ✖ | ✖ | ✔ |
| Pregled izveštaja | ✖ | ✔ | ✔ |

Da bismo ovo pravilno implementirali koristićemo **OOP principe**.

---

# 2️⃣ Enum – dozvole sistema

Potrebno je napraviti **enum `Dozvola`** koji predstavlja sve akcije koje korisnik može da izvrši u sistemu.

Primer:

- Pregled
- Dodavanje
- Izmena
- Brisanje
- Izvestaj

Enum služi da **na jednom mestu definišemo sve moguće akcije u sistemu**.

---

# 3️⃣ Interfejs `IDozvola`

Potrebno je napraviti interfejs:

```csharp
public interface IDozvola
{
    bool ImaDozvolu(Dozvola dozvola);
}
```

Ova metoda proverava **da li korisnik ima određenu dozvolu**.

Svaka klasa koja implementira ovaj interfejs mora definisati pravila dozvola.

---

# 4️⃣ Apstraktna klasa `Korisnik`

Potrebno je napraviti **apstraktnu klasu Korisnik**.

Zajedničke osobine:

- Username
- Password
- Ime
- Prezime

Klasa implementira interfejs `IDozvola`, ali pravila dozvola definišu izvedene klase.

```csharp
public abstract class Korisnik : IDozvola
{
    public string Username { get; set; }
    public string Password { get; set; }

    protected Korisnik(string username, string password)
    {
        Username = username;
        Password = password;
    }

    public abstract bool ImaDozvolu(Dozvola dozvola);
}
```

---

# 5️⃣ Nasleđivanje korisnika

Tri klase nasleđuju klasu **Korisnik**:

- Gost
- KorisnikSistema
- Administrator

Svaka implementira metodu:

```csharp
ImaDozvolu(Dozvola dozvola)
```

Primer: Gost ima samo dozvolu za pregled.

```csharp
public class Gost : Korisnik
{
    public Gost(string username, string password)
        : base(username, password)
    {
    }

    public override bool ImaDozvolu(Dozvola dozvola)
    {
        return dozvola == Dozvola.Pregled;
    }
}
```

---

# 6️⃣ Polimorfizam

U aplikaciji se koristi lista:

```csharp
List<Korisnik>
```

U njoj mogu biti objekti:

- Gost
- KorisnikSistema
- Administrator

Svi se tretiraju kao tip **Korisnik**.

To je primer **polimorfizma**.

---

# 7️⃣ Korišćenje dozvola u aplikaciji

Kada korisnik izvrši akciju, proverava se dozvola:

```csharp
korisnik.ImaDozvolu(Dozvola.Brisanje)
```

Ako nema dozvolu:

- akcija se ne izvršava
- prikazuje se poruka o grešci

---

# 8️⃣ Podešavanje dugmadi na formi

Kada se korisnik prijavi:

- dugmad se omogućavaju ili onemogućavaju prema dozvolama.

Primer:

- ako korisnik nema dozvolu za brisanje → dugme **Obriši** je onemogućeno
- ako nema dozvolu za dodavanje → dugme **Dodaj** je onemogućeno

Forma **ne proverava tip korisnika**, već poziva:

```csharp
ImaDozvolu(...)
```

---

# 9️⃣ Važno pravilo u projektu

Forma **ne proverava da li je korisnik administrator**.

Forma samo pita:

> Da li imaš ovu dozvolu?

Ovo omogućava **čist i fleksibilan kod**.

---

# 🔟 Koncepti koji se primenjuju

| Koncept | Objašnjenje |
|------|------|
| Apstraktna klasa | zajedničke osobine korisnika |
| Nasleđivanje | različite vrste korisnika |
| Interfejs | proverava dozvole |
| Enum | definiše akcije sistema |
| Polimorfizam | svi korisnici koriste tip Korisnik |

---

# 🎓 Cilj zadatka

Učenici treba da nauče:

- kako se koristi apstraktna klasa
- kako se implementira interfejs
- kako funkcioniše nasleđivanje
- kako se koristi enum
- kako funkcioniše polimorfizam
- kako se pravi sistem korisničkih dozvola

---

# 📦 Kompletan mini primer modela

## Dozvola.cs

```csharp
namespace SkolskaEvidencija.Models
{
    public enum Dozvola
    {
        Pregled,
        Dodavanje,
        Izmena,
        Brisanje,
        Izvestaj
    }
}
```

## IDozvola.cs

```csharp
namespace SkolskaEvidencija.Models
{
    public interface IDozvola
    {
        bool ImaDozvolu(Dozvola dozvola);
    }
}
```

## Korisnik.cs

```csharp
namespace SkolskaEvidencija.Models
{
    public abstract class Korisnik : IDozvola
    {
        public string Username { get; set; }
        public string Password { get; set; }

        protected Korisnik(string username, string password)
        {
            Username = username;
            Password = password;
        }

        public abstract bool ImaDozvolu(Dozvola dozvola);
    }
}
```

## Gost.cs

```csharp
namespace SkolskaEvidencija.Models
{
    public class Gost : Korisnik
    {
        public Gost(string username, string password)
            : base(username, password)
        {
        }

        public override bool ImaDozvolu(Dozvola dozvola)
        {
            return dozvola == Dozvola.Pregled;
        }
    }
}
```

## KorisnikSistema.cs

```csharp
namespace SkolskaEvidencija.Models
{
    public class KorisnikSistema : Korisnik
    {
        public KorisnikSistema(string username, string password)
            : base(username, password)
        {
        }

        public override bool ImaDozvolu(Dozvola dozvola)
        {
            return dozvola == Dozvola.Pregled ||
                   dozvola == Dozvola.Dodavanje ||
                   dozvola == Dozvola.Izmena ||
                   dozvola == Dozvola.Izvestaj;
        }
    }
}
```

## Administrator.cs

```csharp
namespace SkolskaEvidencija.Models
{
    public class Administrator : Korisnik
    {
        public Administrator(string username, string password)
            : base(username, password)
        {
        }

        public override bool ImaDozvolu(Dozvola dozvola)
        {
            return true;
        }
    }
}
```
