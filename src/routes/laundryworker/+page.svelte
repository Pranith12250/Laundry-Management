<script>
  import firebase from 'firebase/compat/app';
  import { initializeApp } from 'firebase/app';
  import { getFirestore, doc, getDoc, setDoc } from 'firebase/firestore';
  
  const firebaseConfig = {
  apiKey: 'AIzaSyAPSYyrNMPFW48-ls7O6EZnHVjv5w9Oxt',
  authDomain: 'laundry-data.firebaseapp.com',
  projectId: 'laundry-data',
  storageBucket: "laundry-data.appspot.com",
  messagingSenderId: "979256368252",
  appId: "1:979256368252:web:f5a8cf9e1ac3b2eaaffdf9",
  measurementId: "G-VC9PBDFCQ2"
  };

  const app = initializeApp(firebaseConfig);

  if (!firebase.apps.length) 
  {
    firebase.initializeApp(firebaseConfig);
  }

  let showData=false;
  let registrationNumber = '';
  let studentName = '';
  let numberOfWashes='';
  let status='';
  async function getStudentSnapshot(registrationNumber) 
  {
    const db = getFirestore(app);
    const studentRef = doc(db, `students/${registrationNumber}`);
    return await getDoc(studentRef);
  }
  function handleFetchStudentData() 
  {
    fetchStudentData(registrationNumber);
  }

  async function fetchStudentData(registrationNumber) 
  {
    const docSnapshot = await getStudentSnapshot(registrationNumber);
    try 
    {

      if (docSnapshot.exists()) 
      {
        studentName = docSnapshot.data().name;
        numberOfWashes=docSnapshot.data().washes;
        status=docSnapshot.data().currentStatus;
        showData=true;
      } 
      else 
      {
        console.log(registrationNumber);
        studentName = 'No student found with this registration number';
        numberOfWashes=0;
        status='Status unavailable';
        showData=true;
      }
    } 
    catch (error) 
    {
      console.error('Error fetching student data:', error);
      studentName = 'Error fetching student data';
      numberOfWashes=0;
      status='Status unavailable';
      showData=true;
    }
  }

  function handleResetWashCycle() 
  {
    resetCycle();
  }
  async function resetCycle()
  {
    const docSnapshot = await getStudentSnapshot(registrationNumber);
    if (docSnapshot.exists()) 
    {
      const studentData = docSnapshot.data();
      const db = getFirestore(app);
      const studentRef = doc(db, `students/${registrationNumber}`);
      await setDoc(studentRef, {
        ...studentData,
        washes: 4,
        currentStatus: 'Open',
      });
      numberOfWashes =4;
      status = 'Open';
    } 
    else 
    {
      console.log('No student found with this registration number');
    }
  }

  function handleStatusChange() 
  {
    changeStatus();
  }
  async function changeStatus()
  {
    let newStatus=status;
    if(status=='in wash')
      newStatus='Ready';
    const docSnapshot = await getStudentSnapshot(registrationNumber);
    if (docSnapshot.exists() && newStatus!=status) 
    {
      const studentData = docSnapshot.data();
      const db = getFirestore(app);
      const studentRef = doc(db, `students/${registrationNumber}`);
      await setDoc(studentRef, {
        ...studentData,
        washes: numberOfWashes,
        currentStatus: newStatus,
      });
      status = newStatus;
    } 
    else 
    {
      console.log('No student found with this registration number');
    }
  }
</script>

<main style="background-color:#f7f3e9">
  <a href='/'>Home</a>
  <h1 style="font-size:60px">Welcome, Laundry Worker!</h1>
  <label for="registrationNumber"style="font-size: 30px">Enter Student Registration Number:</label>
  <input type="text" id="registrationNumber" bind:value={registrationNumber} />
  <div class="button-container">
    <button on:click={handleFetchStudentData}>
      <span style="font-size: 30px;">Fetch Student data </span>
    </button>
  </div>

  {#if showData}
    <h1 style="font-style:20px"><br>Your data:<br></h1>
    <h2 style="font-style:20px">Name: {studentName} <br></h2>
    <h2 style="font-style:20px"> Number of washes remaining: {numberOfWashes}</h2>
    <h2 style="font-style:20px"> Current laundry status: {status}</h2>
  {/if}

  {#if showData && numberOfWashes<4}
    <div class="button-container">
      <button on:click={handleResetWashCycle}>
        <span style="font-size: 20px;">Reset wash cycle </span>
      </button>
    </div>
  {/if}

  {#if showData && status=='in wash'}
    <div class="button-container">
      <button on:click={handleStatusChange}>
        <span style="font-size: 20px;">Change wash status</span>
      </button>
    </div>
  {/if}
</main>

<style>
  main 
  {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    height: 100vh;
    overflow: auto;
    padding-top: 50px;
    padding-bottom: 50px;
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

  input[type="text"] 
  {
    margin-bottom: 20px;
    padding: 10px;
    border: 2px solid #3498db;
    border-radius: 10px;
    transition: border-color 0.3s, transform 0.2s;
    font-size: 18px;
  }
  input[type="text"]:hover 
  {
    border-color: #e06b48;
    transform: scale(1.05);
  }
  input[type="text"]:focus 
  {
    outline: none;
    border-color: #e06b48;
    transform: scale(1.05);
  }

  label 
  {
    margin-bottom: 10px;
    font-size: 30px;
  }
</style>
