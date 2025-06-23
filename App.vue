<template>
  <div class="app">
    <h1>CRUD de Usuarios</h1>

  
    <form @submit.prevent="guardarUsuario">
      <input v-model="formulario.name" placeholder="Nombre" required>
      <input v-model="formulario.username" placeholder="Username" required>
      <input v-model="formulario.email" placeholder="Email" type="email" required>
      <input v-model="formulario.phone" placeholder="Teléfono">
      <button type="submit">{{ editando ? 'Guardar' : 'Agregar' }}</button>
      <button v-if="editando" @click="cancelarEdicion">Cancelar</button>
    </form>

    <table v-if="!cargando">
      <thead>
        <tr>
          <th>Nombre</th>
          <th>Username</th>
          <th>Email</th>
          <th>Teléfono</th>
          <th>Acciones</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="usuario in usuarios" :key="usuario.id">
          <td>{{ usuario.name }}</td>
          <td>{{ usuario.username }}</td>
          <td>{{ usuario.email }}</td>
          <td>{{ usuario.phone }}</td>
          <td>
            <button @click="editarUsuario(usuario)">✏️ Editar</button>
            <button @click="eliminarUsuario(usuario.id)">🗑️ Eliminar</button>
          </td>
        </tr>
      </tbody>
    </table>
    <p v-else>Cargando usuarios...</p>
  </div>
</template>

<script>
export default {
  data() {
    return {
      usuarios: [],
      cargando: true,
      editando: false,
      formulario: {
        id: null,
        name: '',
        username: '',
        email: '',
        phone: ''
      }
    }
  },
  async created() {
    const response = await fetch('https://jsonplaceholder.typicode.com/users');
    this.usuarios = await response.json();
    this.cargando = false;
  },
  methods: {
    guardarUsuario() {
      if (this.editando) {
        const index = this.usuarios.findIndex(u => u.id === this.formulario.id);
        this.usuarios[index] = { ...this.formulario };
      } else {
        this.usuarios.unshift({
          ...this.formulario,
          id: Math.max(...this.usuarios.map(u => u.id), 0) + 1
        });
      }
      this.limpiarFormulario();
    },
    editarUsuario(usuario) {
      this.formulario = { ...usuario };
      this.editando = true;
    },
    eliminarUsuario(id) {
      if (confirm('¿Eliminar este usuario?')) {
        this.usuarios = this.usuarios.filter(u => u.id !== id);
      }
    },
    cancelarEdicion() {
      this.editando = false;
      this.limpiarFormulario();
    },
    limpiarFormulario() {
      this.formulario = {
        id: null,
        name: '',
        username: '',
        email: '',
        phone: ''
      };
    }
  }
}
</script>

<style>
:root {
  --primary: #4361ee;
  --primary-light: #5a7dff;
  --secondary: #4cc9f0;
  --background: #ffffff;
  --text: #2b2d42;     
  --text-light: #6c757d; 
  --danger: #ef233c;
  --success: #2ec4b6;
  --border: #e9ecef;
}

.app {
  max-width: 1000px;
  margin: 2rem auto;
  padding: 0 1rem;
  font-family: 'Inter', system-ui, sans-serif;
  background-color: var(--background);
  color: var(--text);
  line-height: 1.6;
}

h1 {
  color: var(--text);
  text-align: center;
  margin-bottom: 2rem;
  font-weight: 700;
  font-size: 2.2rem;
  position: relative;
  padding-bottom: 0.5rem;
}

h1::after {
  content: '';
  position: absolute;
  bottom: 0;
  left: 50%;
  transform: translateX(-50%);
  width: 100px;
  height: 3px;
  background: var(--primary);
}

form {
  background: var(--background);
  padding: 1.5rem;
  border-radius: 8px;
  box-shadow: 0 2px 15px rgba(0, 0, 0, 0.05);
  margin-bottom: 2rem;
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
  gap: 1.2rem;
  border: 1px solid var(--border);
}

input {
  padding: 0.8rem;
  border: 1px solid var(--border);
  border-radius: 6px;
  font-size: 1rem;
  transition: all 0.3s;
  background-color: var(--background);
  color: var(--text);
}

input::placeholder {
  color: var(--text-light);
  opacity: 0.7;
}

input:focus {
  border-color: var(--primary-light);
  outline: none;
  box-shadow: 0 0 0 3px rgba(67, 97, 238, 0.1);
}

button {
  padding: 0.8rem 1.5rem;
  border: none;
  border-radius: 6px;
  cursor: pointer;
  font-weight: 600;
  transition: all 0.2s;
  font-size: 0.95rem;
}

button[type="submit"] {
  background-color: var(--primary);
  color: white;
}

button[type="submit"]:hover {
  background-color: var(--primary-light);
}

button:not([type="submit"]) {
  background-color: #f1f3f5;
  color: var(--text);
}

button:not([type="submit"]):hover {
  background-color: #e2e6ea;
}

table {
  width: 100%;
  border-collapse: collapse;
  background: var(--background);
  box-shadow: 0 2px 15px rgba(0, 0, 0, 0.05);
  border-radius: 8px;
  overflow: hidden;
  border: 1px solid var(--border);
}

th, td {
  padding: 1.2rem 1rem;
  text-align: left;
  border-bottom: 1px solid var(--border);
}

th {
  background-color: #f8f9fa;
  color: var(--text);
  font-weight: 600;
  font-size: 0.9rem;
  letter-spacing: 0.3px;
}

td {
  color: var(--text);
}

tr:hover {
  background-color: #f8fafc;
}

.actions {
  display: flex;
  gap: 0.5rem;
}

.actions button {
  padding: 0.5rem 0.8rem;
  min-width: 40px;
  border-radius: 4px;
  font-size: 0.9rem;
}

.actions button:first-child {
  background-color: #fff3bf;
  color: #5f3f16;
}

.actions button:first-child:hover {
  background-color: #ffec99;
}

.actions button:last-child {
  background-color: #ffdce0;
  color: var(--danger);
}

.actions button:last-child:hover {
  background-color: #ffc9d2;
}

.loading {
  text-align: center;
  padding: 2rem;
  color: var(--primary);
  font-size: 1.1rem;
}

@media (max-width: 768px) {
  form {
    grid-template-columns: 1fr;
  }
  
  th, td {
    padding: 0.8rem;
  }
}
</style>
