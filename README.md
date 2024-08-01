# Formularz Zamówień - Aplikacja Offline

## Opis

Formularz zamówień to prosty interaktywny formularz HTML umożliwiający składanie zamówień na produkty dostępne w katalogu PDF. Jest to rozwiązanie offline, które pozwala na składanie zamówień bez potrzeby korzystania z serwera i API.

## Funkcjonalności

- **Wybór kategorii**: Użytkownik może wybierać między różnymi kategoriami produktów (Napoje, Chemia, Alkohol) z rozwijanej listy.
- **Wyświetlanie produktów**: Po wyborze kategorii, produkty przypisane do danej kategorii są wyświetlane w tabeli z możliwością wprowadzenia ilości.
- **Wprowadzanie ilości**: Użytkownik może wprowadzać ilości zamawianych produktów w polach liczbowych.
- **Wysyłanie zamówienia**: Po uzupełnieniu formularza, użytkownik może kliknąć przycisk „Wyślij zamówienie”, co otworzy domyślny klient e-mailowy z przygotowanym e-mailem zawierającym szczegóły zamówienia.

## Instrukcja użytkowania

1. **Wybierz kategorię**: Wybierz interesującą Cię kategorię produktów z rozwijanej listy.
2. **Wprowadź ilości**: Wprowadź ilości zamawianych produktów w odpowiednich polach liczbowych.
3. **Wypełnij formularz**: Uzupełnij pozostałe wymagane pola, takie jak nazwa firmy, NIP, adres, imię, telefon i e-mail.
4. **Wyślij zamówienie**: Kliknij przycisk „Wyślij zamówienie”, aby otworzyć domyślny klient e-mailowy z przygotowanym e-mailem.

## Jak działa

Formularz jest napisany w HTML i JavaScript i działa całkowicie w przeglądarce, bez potrzeby serwera ani API. Skrypt JavaScript obsługuje:
- **Ukrywanie i pokazywanie kategorii**: Po zmianie wyboru kategorii, skrypt ukrywa wszystkie kategorie i pokazuje tylko wybraną.
- **Walidację formularza**: Skrypt sprawdza, czy pole „Nazwa firmy” jest wypełnione i wyświetla alert, jeśli jest puste.
- **Tworzenie e-maila**: Skrypt generuje link `mailto:` z przygotowanym e-mailem, który zawiera szczegóły zamówienia i otwiera domyślny klient e-mailowy.

```javascript
document.getElementById('categorySelect').addEventListener('change', function(event) {
    document.querySelectorAll('.category').forEach(function(category) {
        category.style.display = 'none';
    });
    var selectedCategory = event.target.value;
    if (selectedCategory !== 'none') {
        document.getElementById('section' + selectedCategory.charAt(0).toUpperCase() + selectedCategory.slice(1)).style.display = 'block';
    }
});

document.getElementById('orderForm').addEventListener('submit', function(event) {
    event.preventDefault();

    let formData = new FormData(this);
    let companyName = formData.get('companyName');
    if (!companyName) {
        alert('Nazwa firmy jest wymagana.');
        return;
    }

    let products = '';

    formData.forEach(function(value, key) {
        if (key !== 'nip' && key !== 'phone') {
            if (value > 0) {
                let product = document.querySelector(`[name=${key}]`).dataset.product;
                products += `${product} - ${value}\n`;
            }
        }
    });

    let mailtoLink = 'mailto:tomasz.pielecki86@gmail.com';
    mailtoLink += `?subject=Zamówienie ${companyName}`;
    mailtoLink += '&body=' + encodeURIComponent(`
        Nazwa firmy: ${companyName}
        NIP: ${formData.get('nip')}
        Adres: ${formData.get('address')}
        Imię: ${formData.get('names')}
        Telefon: ${formData.get('phone')}
        Email: ${formData.get('email')}
        Produkty:
        ${products}
    `);

    window.location.href = mailtoLink;
});
