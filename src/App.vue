<template>
  <div id="app">
    <h1>Справочник организаций</h1>
    <!-- Поле для поиска организаций по имени директора -->
    <input
      v-model="searchQuery"
      placeholder="Найти по ФИО"
      class="search-input"
    />
    <!-- Кнопка для открытия модального окна добавления организации -->
    <button @click="showAddModal = true" class="add-button">Добавить</button>
    <table class="organization-table">
      <thead>
        <tr>
          <!-- Заголовки таблицы, с возможностью сортировки по имени директора -->
          <th @click="sortBy('name')">Название</th>
          <th
            @click="sortBy('director')"
            :class="{
              'sorted-asc': sortKey === 'director' && sortOrder === 1,
              'sorted-desc': sortKey === 'director' && sortOrder === -1,
            }"
          >
            ФИО директора
            <span v-if="sortKey === 'director'">
              <span v-if="sortOrder === 1"> ▲</span>
              <span v-else> ▼</span>
            </span>
          </th>
          <th>Номер телефона</th>
          <th class="actions">Действия</th>
        </tr>
      </thead>
      <tbody>
        <!-- Отображение списка организаций, с возможностью удаления -->
        <tr
          v-for="(organization, index) in paginatedData"
          :key="organization.id"
        >
          <td>{{ organization.name }}</td>
          <td>{{ organization.director }}</td>
          <td>{{ organization.phone }}</td>
          <td class="actions">
            <button @click="removeOrganization(index)" class="delete-button">
              Удалить
            </button>
          </td>
        </tr>
      </tbody>
    </table>
    <!-- Компонент пагинации -->
    <pagination
      :total="filteredData.length"
      :page-size="pageSize"
      @page-changed="onPageChange"
    />
    <!-- Модальное окно добавления организации -->
    <add-modal
      v-if="showAddModal"
      @close="showAddModal = false"
      @add="addOrganization"
    />
  </div>
</template>

<script>
import Pagination from "./components/Pagination.vue";
import AddModal from "./components/AddModal.vue";

export default {
  components: {
    Pagination,
    AddModal,
  },
  data() {
    return {
      organizations: [], // Массив организаций
      searchQuery: "", // Строка поиска
      sortKey: "", // Ключ для сортировки
      sortOrder: 1, // Порядок сортировки: 1 - по возрастанию, -1 - по убыванию
      currentPage: 1, // Текущая страница для пагинации
      pageSize: 10, // Количество элементов на странице
      showAddModal: false, // Флаг отображения модального окна добавления
    };
  },
  mounted() {
    // Загрузка данных организаций из localStorage при монтировании компонента
    const savedOrganizations = localStorage.getItem("organizations");
    if (savedOrganizations) {
      this.organizations = JSON.parse(savedOrganizations);
    }
  },
  computed: {
    // Фильтрация данных по строке поиска
    filteredData() {
      const query = this.searchQuery.trim().toLowerCase();
      if (!query) {
        return this.organizations;
      }
      return this.organizations.filter((org) =>
        org.director.toLowerCase().includes(query)
      );
    },
    // Сортировка данных
    sortedData() {
      const data = [...this.filteredData];
      if (this.sortKey === "director") {
        data.sort((a, b) => {
          const nameA = a.director.toLowerCase();
          const nameB = b.director.toLowerCase();
          return this.sortOrder === 1
            ? nameA.localeCompare(nameB)
            : nameB.localeCompare(nameA);
        });
      }
      return data;
    },
    // Пагинация данных
    paginatedData() {
      const start = (this.currentPage - 1) * this.pageSize;
      return this.sortedData.slice(start, start + this.pageSize);
    },
  },
  methods: {
    // Метод для сортировки по заданному ключу
    sortBy(key) {
      if (this.sortKey === key) {
        this.sortOrder = -this.sortOrder;
      } else {
        this.sortKey = key;
        this.sortOrder = 1;
      }
    },
    // Метод для смены страницы
    onPageChange(page) {
      this.currentPage = page;
    },
    // Метод для добавления новой организации
    addOrganization(organization) {
      this.organizations.push(organization);
      this.saveOrganizations();
      this.showAddModal = false;
    },
    // Метод для удаления организации
    removeOrganization(index) {
      this.organizations.splice(index, 1);
      this.saveOrganizations();
    },
    // Метод для сохранения организаций в localStorage
    saveOrganizations() {
      localStorage.setItem("organizations", JSON.stringify(this.organizations));
    },
  },
};
</script>

<style scoped>
#app {
  font-family: Arial, sans-serif;
  margin: 20px;
}

h1 {
  color: #333;
}

.search-input {
  margin-bottom: 20px;
  padding: 10px;
  width: 200px;
  border: 1px solid #ccc;
  border-radius: 4px;
}

.add-button {
  padding: 10px 20px;
  margin-bottom: 20px;
  background-color: #28a745;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

.add-button:hover {
  background-color: #218838;
}

.organization-table {
  width: 100%;
  border-collapse: collapse;
}

.organization-table th,
.organization-table td {
  border: 1px solid #ddd;
  padding: 8px;
  text-align: left;
}

.organization-table th {
  background-color: #f2f2f2;
  cursor: pointer;
}

.organization-table th.actions,
.organization-table td.actions {
  text-align: center;
}

.delete-button {
  padding: 5px 10px;
  background-color: #dc3545;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

.delete-button:hover {
  background-color: #c82333;
}
</style>
