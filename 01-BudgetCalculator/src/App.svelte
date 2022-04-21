<script>
  import { setContext, onMount, afterUpdate } from 'svelte';

  // components
  import Navbar from './Navbar.svelte';
  import ExpensesList from './ExpensesList.svelte';
  import Totals from './Totals.svelte';
  import ExpenseForm from './ExpenseForm.svelte';
  import Modal from './Modal.svelte';

  // data
  // import expensesData from './expenses';
  let expenses = [];
  // variables

  // set editing variables
  let setName = '';
  let setAmount = null;
  let setId = null;

  // toggle form variable
  let isFormOpen = false;

  // reactive
  $: isEditing = setId ? true : false;
  $: total = expenses.reduce((acc, curr) => {
    return (acc += curr.amount);
  }, 0);

  // functions
  function removeExpense(id) {
    expenses = expenses.filter((item) => item.id !== id);
  }

  function clearExpenses() {
    expenses = [];
  }

  function addExpense({ name, amount }) {
    let expense = { id: Math.random() * Date.now(), name, amount };
    expenses = [expense, ...expenses];
  }

  function setModifiedExpense(id) {
    let expense = expenses.find((item) => item.id === id);
    setId = expense.id;
    setName = expense.name;
    setAmount = expense.amount;
    showForm();
  }

  function editExpense({ name, amount }) {
    expenses = expenses.map((item) => {
      return item.id === setId ? { ...item, name, amount } : { ...item };
    });
    setId = null;
    setAmount = null;
    setName = '';
  }

  function showForm() {
    isFormOpen = true;
  }

  function closeForm() {
    isFormOpen = false;
    setName = '';
    setAmount = null;
    setId = null;
  }

  // context
  setContext('remove', removeExpense);
  setContext('modify', setModifiedExpense);

  // localstorage
  function setLocalStorage() {
    localStorage.setItem('expenses', JSON.stringify(expenses));
  }

  // Cylelife
  onMount(() => {
    expenses = localStorage.getItem('expenses')
      ? JSON.parse(localStorage.getItem('expenses'))
      : [];
  });

  afterUpdate(() => {
    setLocalStorage();
  });
</script>

<Navbar {showForm} />
<main class="content">
  {#if isFormOpen}
    <Modal>
      <ExpenseForm
        {addExpense}
        name={setName}
        amount={setAmount}
        {isEditing}
        {editExpense}
        {closeForm}
      />
    </Modal>
  {/if}
  <Totals title="total expenses" {total} />
  <ExpensesList {expenses} />
  <button
    type="button"
    class="btn btn-block btn-primary"
    on:click={clearExpenses}>clear expenses</button
  >
</main>
