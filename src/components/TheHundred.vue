<template>
  <div class="container">
    <img src="../assets/title.png" alt="">
    <!-- Kurallar Modalı -->
    <modal name="rules-modal" :adaptive="true" :scrollable="true" :width="800">
      <h2>Kurallar</h2>
      <p>
        1. Oyun, 10x10'luk bir kutu içerisinde gerçekleşir.<br>
        2. Kutulara sırasıyla 1'den 100'e kadar numaralar yerleştirilir.<br>
        3. Yatay veya dikey ilerlemelerde 2 kutu boşluk bırakılmalıdır, çapraz ilerlemelerde ise 1 kutu boşluk bırakılmalıdır.<br>
        4. Bir kutu işaretlendiğinde ilerlenebilecek kutular mavi renk ile gösterilir.<br>
        5. Oyun, tüm numaralar yerleştirildiğinde tamamlanır.<br>
        6. Oyunu sıfırlamak için "Yeniden Başlat" butonuna tıklayabilirsiniz.<br>
        7. Yapılan hamleyi geri almak için "Hamleyi Geri Al" butonuna tıklayabilirsiniz.<br>
        8. Oyunun kurallarını her zaman bu modal penceresinden görebilirsiniz.<br>
      </p>
    </modal>
    <table>
      <tbody>
        <tr v-for="(row, rowIndex) in table" :key="rowIndex">
          <td v-for="(cell, colIndex) in row" :key="colIndex" :class="{
            'selected': cell !== null && isCellSelected(rowIndex, colIndex),
            'success': success && cell !== null,
            'fail': gameOver && cell !== null,
            'clickable': isCellClickable(rowIndex, colIndex)
          }" @click="selectCell(rowIndex, colIndex)">
            {{ cell }}
          </td>
        </tr>
      </tbody>
    </table>
    <div v-if="gameOver">
      <h2 v-if="success" class="success">Tebrikler! Bulmacayı başarıyla tamamladınız.</h2>
      <h2 v-else class="fail">Üzgünüz! Bulmacayı tamamlayamadınız.</h2>
    </div>
    <button @click="undoMove" class="action-button" v-if="selectedCells.length > 0">
      <i class="fas fa-undo-alt"></i>
    </button>
    <button @click="openModal">Kurallar</button>
    <button @click="resetGame" class="reset-button">
      <i class="fas fa-sync-alt"></i>
    </button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      table: [],
      selectedCells: [],
      gameOver: false,
      success: false,
      showModal: true
    };
  },
  mounted() {
    this.initializeTable();
    this.openModal();
  },
  methods: {
    openModal() {
      this.$modal.show('rules-modal');
    },
    initializeTable() {
      for (let i = 0; i < 10; i++) {
        const row = [];
        for (let j = 0; j < 10; j++) {
          row.push(null);
        }
        this.table.push(row);
      }
    },
    selectCell(rowIndex, colIndex) {
      if (this.gameOver || !this.isCellClickable(rowIndex, colIndex)) {
        return;
      }

      const selectedCell = this.table[rowIndex][colIndex];
      if (selectedCell !== null) {
        return;
      }

      this.table[rowIndex].splice(colIndex, 1, this.getNextNumber());
      this.selectedCells.push({ rowIndex, colIndex });
      if (this.selectedCells.length === 100) {
        this.gameOver = true;
        this.success = true;
      }
    },
    isCellSelected(rowIndex, colIndex) {
      const cell = this.selectedCells.find(
        (cell) => cell.rowIndex === rowIndex && cell.colIndex === colIndex
      );
      return cell !== undefined;
    },
    isCellClickable(rowIndex, colIndex) {
      const cell = this.table[rowIndex][colIndex];
      if (cell !== null) {
        return false; // Dolu kutucuklar tıklanamaz
      }

      if (this.selectedCells.length === 0) {
        return true; // İlk tıklama her zaman tıklanabilir
      }

      const lastCell = this.selectedCells[this.selectedCells.length - 1];
      const rowDiff = Math.abs(rowIndex - lastCell.rowIndex);
      const colDiff = Math.abs(colIndex - lastCell.colIndex);

      if (
        ((rowDiff === 0 && colDiff === 3) || (rowDiff === 3 && colDiff === 0)) || // Yatay veya dikey olarak 3 kutucuk boş bırakma
        (rowDiff === 2 && colDiff === 2) // Çapraz olarak 1 kutucuk boş bırakma
      ) {
        return true;
      }

      return false;
    },
    getNextNumber() {
      const lastNumber = this.selectedCells.length;
      return lastNumber + 1;
    },
    undoMove() {
      const lastCell = this.selectedCells.pop();
      const rowIndex = lastCell.rowIndex;
      const colIndex = lastCell.colIndex;
      this.table[rowIndex].splice(colIndex, 1, null);
    },
    resetGame() {
      this.table = [];
      this.selectedCells = [];
      this.gameOver = false;
      this.success = false;
      this.initializeTable();
    }
  }
};
</script>

<style>
.container {
  max-width: 500px;
  margin: 0 auto;
}

table {
  width: 100%;
  border-collapse: collapse;
  margin-bottom: 10px;
}

td {
  width: 50px;
  height: 50px;
  text-align: center;
  border: 1px solid black;
  cursor: pointer;
}

.selected {
  background-color: yellow;
}

.success {
  background-color: green;
  color: white;
  padding: 10px;
  text-align: center;
}

.fail {
  background-color: red;
  color: white;
  padding: 10px;
  text-align: center;
}

.clickable {
  background-color: lightblue;
}

button {
  display: inline-block;
  padding: 10px 20px;
  margin-right: 10px;
  font-size: 16px;
  font-weight: bold;
  text-transform: uppercase;
  border: 2px solid #4caf50;
  border-radius: 4px;
  background-color: transparent;
  color: #4caf50;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

button:hover {
  background-color: #4caf50;
  color: white;
}

button:active {
  background-color: #45a049;
}

.modal {
  background-color: #fff;
  padding: 20px;
  border-radius: 4px;
}

.modal h2 {
  margin-top: 0;
  font-size: 24px;
  font-weight: bold;
}

.modal p {
  margin-bottom: 10px;
  font-size: 16px;
  line-height: 1.5;
}

.modal ul {
  margin: 0;
  padding: 0;
  list-style-type: none;
}

.modal ul li {
  margin-bottom: 5px;
  font-size: 16px;
  line-height: 1.5;
}

.modal ul li:before {
  content: "•";
  margin-right: 5px;
}


.reset-button {
  padding: 8px 10px 9px 10px;
  font-size: 18px;
}

.action-button {
  padding: 8px 10px 9px 10px;
  font-size: 18px;
}

</style>
