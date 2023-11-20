<script>
  
  import firebase from 'firebase/compat/app';
  import { initializeApp } from 'firebase/app';
  import { getFirestore, doc, getDoc, setDoc } from 'firebase/firestore';
  
  const firebaseConfig = {
  apiKey: 'AIzaSyAPSYyrNMPFW48-ls7O6EZnHVjv5w9OxtQ',
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

  function handleFetchStudentData() 
  {
    fetchStudentData(registrationNumber);
  }

  async function getStudentSnapshot(registrationNumber) 
  {
    const db = getFirestore(app);
    const studentRef = doc(db, `students/${registrationNumber}`);
    return await getDoc(studentRef);
  }

  async function giveForWash() 
  {
    const docSnapshot = await getStudentSnapshot(registrationNumber);
    if (docSnapshot.exists()) 
    {
      const studentData = docSnapshot.data();
      const db = getFirestore(app);
      const studentRef = doc(db, `students/${registrationNumber}`);
      await setDoc(studentRef, {
        ...studentData,
        washes: numberOfWashes - 1,
        currentStatus: 'in wash',
      });
      numberOfWashes = numberOfWashes - 1;
      status = 'in wash';
    } 
    else 
    {
      console.log('No student found with this registration number');
    }
  }

  function handleModifyStatus()
  {
    giveForWash();
  }

  async function collectLaundry() 
  {
    const docSnapshot = await getStudentSnapshot(registrationNumber);
    if (docSnapshot.exists()) 
    {
      const studentData = docSnapshot.data();
      const db = getFirestore(app);
      const studentRef = doc(db, `students/${registrationNumber}`);
      await setDoc(studentRef, {
        ...studentData,
        washes: numberOfWashes ,
        currentStatus: 'Open',
      });
      status = 'Opem';
    } 
    else 
    {
      console.log('No student found with this registration number');
    }
  }

  function handleLaundryCollection()
  {
    collectLaundry();
  }
</script>

<main style="background-color:#f7f3e9">
<a href='/'>Home</a>
  <h1 style="font-size:60px">Welcome {studentName}!</h1>
  
  <label for="registrationNumber">Enter Registration Number:</label>
  <input type="text" id="registrationNumber" bind:value={registrationNumber} />
  <button on:click={handleFetchStudentData}>Fetch Student Name</button>

  {#if showData}
    <p> <br>Your data:<br> Name: {studentName} <br></p>
    <p> Number of washes remaining: {numberOfWashes}</p>
    <p> Current laundry status: {status}</p>
  {/if}

  {#if showData && status=='Open' && numberOfWashes>0}
    <button on:click={handleModifyStatus}> Give for wash </button>
  {/if}

  {#if showData && status=='Ready'}
    <button on:click={handleLaundryCollection}>Collect Laundry</button>
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
  }
</style>