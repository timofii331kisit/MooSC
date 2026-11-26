# MooS Calculator v0.11.8 (Stalzone)
**Language:** [Русский](README_ru.md) | English | [Українська](README_ua.md)
**Author:** 2TheMoon / TheMoom  
**Requested by:** Serega_Aspect  

A desktop utility for calculating the total value of in-game items from screenshots for **Stalzone**. Copy a screenshot to your clipboard and press `Ctrl+V` — the application will automatically detect item slots, recognize item names and stack quantities, and calculate the total sum. It also features a built-in calculator with screenshot OCR support.

> **Important Notes:**
> * The **Mail ("Почта")** mode is calibrated specifically for the Stalzone mail/storage grid UI.
> * The **Trade ("Торговля")** tab and screenshot number recognition require **Windows 10/11** with the built-in Windows OCR language pack installed.

---

## Operation Modes (Tabs)

1. **Mail ("Почта")** — visual inventory grid analysis: detects slot boundaries, matches item icons by shape and color (`items/`), and reads stack counts using digit templates (`digits/`).
2. **Trade ("Торговля")** — text-based parsing of trade lists formatted as `Item Name x 126` via Windows OCR, featuring fuzzy matching and self-learning typo correction.
3. **Calculator ("Калькулятор")** — built-in calculator (`+`, `−`, `×`, `÷`, `%`, `±`) with standard operator precedence, one-click total import from the table, and OCR number pasting from screenshots.

---

## Directory & File Structure

The following files and folders are located next to the executable (`MooSC v0.11.8.exe` or `calculator.py`):

| File / Folder | Description |
| :--- | :--- |
| `MooSC v0.11.8.exe` | Main application executable. |
| `prices.txt` | Item price database formatted as `Item Name=price`. |
| `items/` | Reference item slot images (`.png`, `.jpg`, `.jpeg`, `.bmp`, `.webp`). |
| `digits/` | Reference digit templates (`0.png`–`9.png`) and auto-learned samples (`3_ex1.png`, etc.). |
| `Errors/` | *Auto-created.* Stores unique unrecognized item slots for later review. |
| `debug/` | *Auto-created.* Exported slot crops, digit glyphs, and OCR preprocessing variants. |
| `trade_aliases.txt` | *Auto-created.* Dictionary of saved OCR corrections (`ocr_misread=Correct Name`). |
| `trade_examples.txt` | *Optional (user-created).* Additional reference item names (one per line) for Trade mode. |

---

## Quick Start & Setup

### 1. Setting Up Prices
The `prices.txt` file may be empty out of the box:
1. Launch the application and click **"Обновить prices.txt" (Update prices.txt)** — all items from `items/` will be added with a default price of `1`.
2. Set actual prices by double-clicking any **"Цена" (Price)** cell in the table, or by editing `prices.txt` directly in a text editor.

### 2. Installing Windows OCR (for Trade Mode & Number Pasting)
If the app reports an error starting text recognition, install the required Windows OCR capability via **PowerShell (Run as Administrator)**:

```powershell
Add-WindowsCapability -Online -Name "Language.OCR~~~ru-RU~0.0.1.0"
```
*(The application supports `ru` and `uk` OCR packs, using the first available one).*

---

## Detailed Tab Guide

### Mail Tab (Icon Recognition)
1. Take a screenshot of the item grid (e.g., using `Win + Shift + S`).
2. Click **"Вставить скриншот" (Paste Screenshot)** or press `Ctrl+V`.
3. The table will display each item's grid position `(row, col)`, thumbnail, name, quantity, unit price, subtotal, visual share of the total value, and match confidence score.

**Row Color Coding:**
* **Green** — the highest-value stack in the current table.
* **Yellow** — item recognized, but no price is set in `prices.txt`.
* **Red** — unrecognized item (shows the closest match candidate if available).

**In-Table Editing & Self-Learning (Double-Click a Cell):**
* **Item ("Предмет"):** Assign a name to an unrecognized item (saves the slot icon to `items/`) or rename an existing item (updates both the filename in `items/` and the entry in `prices.txt`).
* **Quantity ("Кол-во"):** Correct a misread stack count. If the number of detected glyphs matches the digit length of your input, the app saves new digit samples to `digits/` (e.g., `5_ex1.png`) and immediately re-evaluates all other slots.
* **Price ("Цена"):** Edit the unit price and save it directly to `prices.txt` (leave empty to remove the price).

> **Note:** Item names cannot start with `#` or `?` and cannot contain `\ / : * ? " < > | =`. To accept a suggested candidate for a single calculation without saving it to `items/`, click **"✔ принять" (Accept)** in the confirmation column.

---

### Trade Tab (Text OCR)
Designed for screenshots of text lists formatted like `Мякоть солевика x 126`.
1. Switch to the **Trade ("Торговля")** tab and paste the screenshot (`Ctrl+V`).
2. The app processes the image through multiple filters (upscaling, vignette removal, sharpening, adaptive thresholding) and matches parsed lines against `prices.txt`, `trade_examples.txt`, and `trade_aliases.txt`.
3. If OCR misreads an item name, double-click the **Item** cell to correct it or accept the suggested candidate — the correction is saved to `trade_aliases.txt` and applied automatically in future scans.

---

### Calculator Tab
A full-featured calculator supporting keyboard input (`0-9`, `+`, `-`, `*`, `/`, `%`, `Enter`/`=`, `Backspace`, `Esc`/`Delete` to clear, `Ctrl+C` to copy).

**Special Features:**
* **← ИТОГО из таблицы (Import Total):** Inserts the total sum from your last Mail or Trade scan.
* **Вставить число (Paste Number / `Ctrl+V`):** Extracts a number from clipboard text or directly from a screenshot (e.g., `8 650 000 рублей` is parsed as `8650000`). The pasted value acts as the current operand without clearing an active expression (e.g., `100 + [paste]`).

---

## Toolbar & Asset Preparation

* **↻ Перезагрузить (Reload):** Reloads `items/`, `digits/`, and `prices.txt`, re-analyzing the current screenshot.
* **Сохранить слоты/цифры (Save Slots/Digits):** Exports cropped grid slots (`debug/slots/`) and digit glyphs (`debug/glyphs/`)—or OCR image variants in Trade mode—into the `debug/` folder. Use this to populate `items/` and `digits/` with clean reference templates.
* **Папка Errors (Errors Folder):** Opens the folder containing auto-saved unrecognized item icons (duplicates are automatically filtered out).
* **Обновить prices.txt (Update prices.txt):** Syncs `prices.txt` with `items/`, adding new items with a price of `1` while preserving existing prices and comments.

---

## Troubleshooting

* **"В буфере обмена нет скриншота" (No screenshot in clipboard)** — ensure you copied an actual image to the clipboard, not a file in File Explorer or plain text.
* **All items are unrecognized** — verify that `items/` contains reference images captured at the exact same in-game UI scale as your screenshot.
* **Quantities always read as `1`** — check the status chip at the top of the window: it should read `цифр: 10/10`. Ensure `0.png` through `9.png` are present in `digits/`.
* **Inaccurate number OCR in Calculator** — crop the screenshot tightly around the target number to exclude surrounding UI elements.
