<script>
  import {goto} from '$app/navigation'
  const handleStudentClick=()=>{
    goto('/student')
  }
  const handleLaundryworkerClick=()=>{
    goto('/laundryworker')
  }
</script>

<main style="background-color:#f7f3e9">
  <h1 style="font-size:60px">Welcome to the Laundry App!</h1>
  <h2 style="font-size:12px"> The database currently only has 20BCE7388 as the entry</h2>
  <p style="font-size:25px">Please select your role:</p>

  <div class="button-container">
    <button on:click={handleStudentClick}>
      <span style="font-size: 50px;">🎓 I'm a Student</span>
    </button>
    <button on:click={handleLaundryworkerClick}>
      <span style="font-size: 50px;">👕 I'm a Laundry Worker</span>
    </button>
  </div>
</main>

<style>
  main 
  {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    height: 100vh;
  }
  button
  {
    border-radius: 20px;
    background-color: #3498db;
    transition: background-color 0.3s, transform 0.2s;
  }
  button:hover 
  {
    background-color: #e06b48;
    transform: scale(1.05);
  }
  button:active 
  {
    transform: scale(0.95);
  }
  .button-container 
  {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 20px;
  }
</style>