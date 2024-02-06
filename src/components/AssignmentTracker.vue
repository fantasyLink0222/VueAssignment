
<template>
<div class="container">
    <h1>Assignment Tracker</h1>
   
    <button @click="showCompleted = !showCompleted">
      {{ showCompleted ? 'Hide' : 'Show' }} Completed Assignments
    </button>
    <ul>
      <li v-for="assignment in filteredAssignments" :key="assignment.id">
        <h2>{{ assignment.title }}</h2>
        <p>Due Date: {{ assignment.dueDate }}</p>
        <p>Days Remaining: {{ calculateDaysRemaining(assignment.dueDate) }} Days</p>
        <p :class="['percent-complete', 
                    assignment.percentComplete === 100 ? 'percent-100' : '', 
                    assignment.percentComplete === 0 ? 'percent-0' : '']">
          Percent Complete: {{ assignment.percentComplete }}%
        </p>
      </li>
    </ul>

    
  </div>

</template>

<script>
import assignments from '@/data/assignments';

export default {
  data() {
    return {
      assignments,
      showCompleted: false
    };
  },
  computed: {
   
    sortedAssignments() {
    return this.assignments.slice().sort((a, b) => {
      // Sort by completion status first
      if (a.percentComplete === 100 && b.percentComplete !== 100) {
        return -1; // a is complete, b is not, a comes first
      }
      if (b.percentComplete === 100 && a.percentComplete !== 100) {
        return 1; // b is complete, a is not, b comes first
      }

      // If both have the same completion status, sort by due date
      const dateA = new Date(a.dueDate);
      const dateB = new Date(b.dueDate);
      return dateA - dateB;
    });
  },

  filteredAssignments() {
    return this.sortedAssignments.filter(a => 
      this.showCompleted || a.percentComplete < 100
    );
  }
  },
  methods: {
    calculateDaysRemaining(dueDate) {
    const today = new Date();
    const due = new Date(dueDate);

    // Check if the due date has passed
    if (due < today) {
      // Due date has passed
      return 0;
    } else {
      // Calculate the difference in days
      const diffTime = Math.abs(due - today);
      const diffDays = Math.ceil(diffTime / (1000 * 60 * 60 * 24));
      return diffDays;
    }
  }
  }
};
</script>

<style scoped>
.container {
  max-width: 800px; /* Adjust as needed */
  margin: auto;
  padding: 1rem;
}

h1 {
  margin-bottom: 1rem;
  position: relative;
}

h1::after {
  content: '';
  position: absolute;
  bottom: 0;
  left: 0;
  width: 100%;
  height: 2px;
  background-color: #000;
}

ul {
  list-style: none;
  padding: 0;
  display: grid;
  grid-template-columns: repeat(2, 1fr); /* 2 columns */
  gap: 1rem; /* Adjust the space between squares */
}

li {
  border: 1px solid #ccc; /* Border for square */
  padding: 1rem;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  height: 200px; /* Fixed height for square */
  background-color: #FFF9C4; /* Background color for square */
  color: #000; /* Text color */
  border-radius: 10px; /* Rounded corners */
}

button {
  margin-top: 1rem;
}

/* Additional styling for responsiveness */
@media (max-width: 600px) {
  ul {
    grid-template-columns: 1fr; /* Single column on small screens */
  }
}

.percent-complete {
  color: #000; /* Default color */
}

.percent-100 {
  color: green; /* Color for 100% completion */
}

.percent-0 {
  color: red; /* Color for 0% completion */
}

</style>
