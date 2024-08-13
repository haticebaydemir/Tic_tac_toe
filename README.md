# Tic_tac_toe
## Projenin kodlarına [buraya tıklayarak](https://colab.research.google.com/drive/1TnlTS5_6TEckmQ2cTC47Cywe31V6KD_5?usp=sharing) ulaşabilirsiniz.

```
import numpy as np
import random
from IPython.display import display, clear_output
from ipywidgets import Button, GridBox, VBox, Layout, Output, HTML

# Tahta boyutunu tanımla
BOARD_SIZE = 3

# Oyuncuları ve boş hücreyi tanımla
players = ['X', 'O']
colors = {'X': 'lightblue', 'O': 'lightcoral'}
current_player = 'X'  # Başlangıçta oyuncu X

# Boş tahta oluştur
board = np.full((BOARD_SIZE, BOARD_SIZE), ' ')

# Çıktı alanı
out = Output()
instructions = HTML(value="")
info = HTML(value="")

def update_instructions(text):
    """Talimatları günceller."""
    instructions.value = text

def update_info(text):
    """Bilgi mesajını günceller."""
    info.value = text

def print_board():
    """Tahtayı metin formatında görsel olarak gösterir."""
    with out:
        out.clear_output(wait=True)
        print("Current Board:")
        for row in board:
            print(' | '.join(row))
            print('-' * (4 * BOARD_SIZE - 1))

def check_winner():
    """Kazanan olup olmadığını kontrol eder."""
    for player in players:
        # Satırlar, sütunlar ve köşegenler kontrolü
        if np.any(np.all(board == player, axis=0)) or np.any(np.all(board == player, axis=1)) or \
           np.all(np.diag(board) == player) or np.all(np.diag(np.fliplr(board)) == player):
            return player
    return None

def is_board_full():
    """Tahtanın dolu olup olmadığını kontrol eder."""
    return not np.any(board == ' ')

def ai_move():
    """Bilgisayarın hamlesini yapar."""
    empty_cells = [(r, c) for r in range(BOARD_SIZE) for c in range(BOARD_SIZE) if board[r, c] == ' ']
    if empty_cells:
        return random.choice(empty_cells)
    return None

def on_button_click(b):
    """Buton tıklama olayını yönetir."""
    global current_player
    
    # Koordinatları al
    try:
        row, col = map(int, b.description.split(','))
    except ValueError:
        return  # Koordinatlar geçerli değilse işlemi atla
    
    if board[row, col] == ' ' and current_player == 'X':
        board[row, col] = current_player
        b.style.button_color = colors[current_player]
        b.description = current_player
        print_board()
        
        winner = check_winner()
        if winner:
            update_instructions(f"Player {winner} kazandı!")
            return
        elif is_board_full():
            update_instructions("Beraberlik!")
            return
        
        # Bilgisayarın hamlesini yap
        move = ai_move()
        if move:
            board[move[0], move[1]] = 'O'
            buttons[move[0]][move[1]].style.button_color = colors['O']
            buttons[move[0]][move[1]].description = 'O'
            print_board()
            
            winner = check_winner()
            if winner:
                update_instructions(f"Player {winner} kazandı!")
                return
            elif is_board_full():
                update_instructions("Beraberlik!")
                return
            
        # Oyuncuları değiştir
        current_player = 'X'

# Butonları oluştur
buttons = []
for row in range(BOARD_SIZE):
    button_row = []
    for col in range(BOARD_SIZE):
        button = Button(description=f'{row},{col}', layout=Layout(width='80px', height='80px', 
                                                                   border='2px solid black', 
                                                                   padding='10px'), 
                        style=dict(button_color='lightgray'), 
                        tooltip=f'Row {row}, Column {col}')
        button.on_click(on_button_click)
        button_row.append(button)
    buttons.append(button_row)

# Tahtayı ekrana yerleştir
button_grid = GridBox(children=[item for sublist in buttons for item in sublist],
                      layout=Layout(grid_template_rows=' '.join(['80px']*BOARD_SIZE),
                                    grid_template_columns=' '.join(['80px']*BOARD_SIZE),
                                    border='2px solid black'))

# Oyuna başlamak için talimatları ve bilgi mesajını göster
instructions_text = (
    "<b>Oyun Talimatları:</b><br>"
    "1. Oyuna başlamak için bir hücreye tıklayın.<br>"
    "2. X oyuncusu ilk hamleyi yapar.<br>"
    "3. Bilgisayar (O) otomatik olarak bir hücreye hamle yapar.<br>"
    "4. Oyunun amacı, satır, sütun veya köşegende üç işareti sıralamaktır.<br>"
    "5. Oyun bitene kadar hücrelere tıklayarak hamle yapabilirsiniz.<br>"
    "6. Oyun kazananı veya berabere durumu ekranda görünecektir."
)

# Kullanıcıya hangi takımda olduğunu belirt
info_text = (
    "<b>Bilgi:</b><br>"
    "Oyun Başlıyor!<br>"
    "Siz: <span style='color: lightblue;'>X</span><br>"
    "Bilgisayar: <span style='color: lightcoral;'>O</span><br>"
    "Hamle yapmaya başlayabilirsiniz!"
)
update_info(info_text)
update_instructions(instructions_text)

display(VBox([info, button_grid, out, instructions]))

```
