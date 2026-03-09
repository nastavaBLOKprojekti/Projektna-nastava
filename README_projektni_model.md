# 📘 PROJEKTNI MODEL ZA C# WINDOWS FORMS APLIKACIJU

Jednostavan model organizacije **Windows Forms projekta** koji razdvaja:

- podatke
- logiku
- rad sa fajlovima
- korisnički interfejs

Cilj ovog modela je da učenici nauče **kako da organizuju kod u realnom softverskom projektu**.

---

# 📑 Sadržaj

- Osnovna ideja
- Arhitektura aplikacije
- Model
- Repository
- Service
- UI (Forme)
- Tok rada aplikacije
- Podela odgovornosti
- Validacija podataka
- Uloge korisnika
- Struktura projekta
- Cilj projekta

---

# 🎯 Osnovna ideja

Kada pravimo **veći projekat u Windows Forms aplikaciji**, nije dobro da sav kod pišemo u formi.

Forma treba da služi samo za:

- unos podataka
- prikaz podataka
- reagovanje na klik dugmeta

Sve ostalo treba raspodeliti u **posebne slojeve projekta**.

---

# 🏗 Arhitektura aplikacije

Aplikacija se deli na **4 osnovna sloja**:

| Sloj | Uloga |
|-----|------|
| Model | predstavlja podatke |
| Repository | čuva i učitava podatke |
| Service | poslovna logika |
| UI | komunikacija sa korisnikom |

---

# 1️⃣ Model

Model predstavlja **objekte u sistemu**.

### Primer modela

```csharp
public class Ucenik
{
    public int Id { get; set; }
    public string ImePrezime { get; set; }
    public double Prosek { get; set; }

    public Ucenik()
    {
    }

    public Ucenik(int id, string imePrezime, double prosek)
    {
        Id = id;
        ImePrezime = imePrezime;
        Prosek = prosek;
    }
}
```

### Važno pravilo

Model:

- ne čita fajl
- ne zna ništa o formi
- ne prikazuje MessageBox

Model **samo predstavlja podatke**.

---

# 2️⃣ Repository

Repository sloj služi za:

- učitavanje podataka iz fajla
- čuvanje podataka u fajl

Sav rad sa fajlovima ide **isključivo u ovaj sloj**.

### Repository interfejs

```csharp
public interface IRepository<T>
{
    List<T> Load();
    void Save(List<T> items);
}
```

### Primer repository klase

```csharp
public class UcenikTxtRepository : IRepository<Ucenik>
{
    private readonly string putanja = "ucenici.txt";

    public List<Ucenik> Load()
    {
        List<Ucenik> lista = new List<Ucenik>();

        if (!File.Exists(putanja))
            return lista;

        string[] linije = File.ReadAllLines(putanja);

        foreach (string linija in linije)
        {
            string[] p = linija.Split(';');

            Ucenik u = new Ucenik(
                int.Parse(p[0]),
                p[1],
                double.Parse(p[2])
            );

            lista.Add(u);
        }

        return lista;
    }

    public void Save(List<Ucenik> items)
    {
        List<string> linije = new List<string>();

        foreach (Ucenik u in items)
        {
            linije.Add($"{u.Id};{u.ImePrezime};{u.Prosek}");
        }

        File.WriteAllLines(putanja, linije);
    }
}
```

---

# 3️⃣ Service

Service sloj predstavlja **logiku sistema**.

Service radi:

- validaciju podataka
- proveru pravila
- dodavanje, izmenu i brisanje
- filtriranje i sortiranje
- proveru dozvola korisnika

### Primer service klase

```csharp
public class UcenikService
{
    private readonly IRepository<Ucenik> repository;

    public UcenikService(IRepository<Ucenik> repository)
    {
        this.repository = repository;
    }

    public List<Ucenik> GetAll()
    {
        return repository.Load();
    }

    public void Dodaj(Ucenik novi)
    {
        List<Ucenik> lista = repository.Load();

        if (string.IsNullOrWhiteSpace(novi.ImePrezime))
            throw new Exception("Ime i prezime je obavezno.");

        if (novi.Prosek < 1 || novi.Prosek > 5)
            throw new Exception("Prosek mora biti između 1 i 5.");

        if (lista.Any(x => x.Id == novi.Id))
            throw new Exception("Učenik sa tim ID već postoji.");

        lista.Add(novi);
        repository.Save(lista);
    }
}
```

---

# 4️⃣ UI (Forme)

UI je deo aplikacije koji **korisnik vidi**.

Forma treba da:

- pročita unos iz kontrola
- pozove Service
- prikaže podatke
- prikaže poruke korisniku

### Primer koda u formi

```csharp
private void btnDodaj_Click(object sender, EventArgs e)
{
    if (!int.TryParse(txtId.Text, out int id))
    {
        MessageBox.Show("ID mora biti broj.");
        return;
    }

    if (!double.TryParse(txtProsek.Text, out double prosek))
    {
        MessageBox.Show("Prosek mora biti broj.");
        return;
    }

    try
    {
        Ucenik u = new Ucenik(id, txtImePrezime.Text, prosek);

        service.Dodaj(u);

        PrikaziPodatke();

        MessageBox.Show("Učenik je dodat.");
    }
    catch (Exception ex)
    {
        MessageBox.Show(ex.Message);
    }
}
```

---

# 🔄 Tok rada aplikacije

```
Korisnik klikne dugme
        ↓
Forma pročita podatke
        ↓
Forma proveri format (TryParse)
        ↓
Forma pozove Service
        ↓
Service proveri pravila
        ↓
Service pozove Repository
        ↓
Repository čuva podatke u fajl
        ↓
Forma osveži DataGridView
```

---

# 📊 Podela odgovornosti

| Sloj | Zadatak |
|-----|------|
| Model | predstavlja podatke |
| Repository | čuva i učitava podatke |
| Service | poslovna logika |
| UI (Forma) | komunikacija sa korisnikom |

---

# ✔ Validacija podataka

Forma proverava **format podataka**:

- int.TryParse()
- double.TryParse()

Service proverava **pravila sistema**:

- da li ID već postoji
- da li je prosek u opsegu
- da li je ime prazno

---

# 👥 Uloge korisnika

| Akcija | Gost | Korisnik | Administrator |
|------|------|------|------|
| Pregled | ✔ | ✔ | ✔ |
| Dodavanje | ✖ | ✔ | ✔ |
| Izmena | ✖ | ✔ | ✔ |
| Brisanje | ✖ | ✖ | ✔ |
| Izveštaj | ✖ | ✔ | ✔ |

---

# 📂 Struktura projekta

```
SkolskaEvidencija
│
├── Models
├── Repositories
├── Services
├── Helpers
├── Forms
├── Data
├── Program.cs
└── App.config
```

---

# 🎓 Cilj projekta

Učenici kroz ovaj model treba da nauče:

- kako se pravi uredna Windows Forms aplikacija
- kako da forma ne bude pretrpana kodom
- kako da razdvoje UI, logiku i čuvanje podataka
- kako izgleda struktura realnog softverskog projekta
- kako da kasnije lakše proširuju aplikaciju

---

# 📌 Najvažnije pravilo

**Forma proverava format  
Service proverava pravila  
Repository čuva podatke  
Model predstavlja podatke**
