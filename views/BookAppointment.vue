
<template>
  <div>
    <nav class="navbar navbar-light bg-white shadow-sm mb-4">
      <div class="container d-flex justify-content-between align-items-center">
        <span class="fw-bold fs-5">Doctor Appointments</span>
        <router-link to="/appointments" class="btn btn-outline-primary">⇆ Switch Page</router-link>
      </div>
    </nav>

    <div class="d-flex justify-content-center align-items-center" style="min-height: 80vh;">
      <div class="card shadow p-5" style="width: 100%; max-width: 700px;">
        <h2 class="text-center mb-4 text-primary">Book an Appointment</h2>
        <form class="row g-3" @submit.prevent="submitAppointment">
          <div class="col-12">
            <input v-model="name" type="text" class="form-control" placeholder="Your Name" required />
          </div>
          <div class="col-12">
            <input v-model="symptoms" type="text" class="form-control" placeholder="Symptoms" required />
          </div>
          <div class="col-12">
            <select v-model="selectedSlot" class="form-select" required>
              <option disabled value="">Select a Time Slot</option>
              <option v-for="slot in slots" :key="slot" :value="slot">
                {{ slot }}
              </option>
            </select>
          </div>
          <div class="col-12">
            <button type="submit" class="btn btn-primary w-100">Book</button>
          </div>
        </form>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "BookAppointment",
  data() {
    return {
      name: "",
      symptoms: "",
      selectedSlot: "",
      slots: []
    };
  },
  mounted() {
    fetch("https://430s2axyj6.execute-api.us-east-1.amazonaws.com/API/Slots")
      .then(res => res.json())
      .then(data => {
        const parsed = JSON.parse(data.body);
        this.slots = parsed.filter(s => !s.isBooked).map(s => s.slot);
      });
  },
  methods: {
    submitAppointment() {
      const payload = {
        patientName: this.name,
        symptoms: this.symptoms,
        slot: this.selectedSlot
      };

      fetch("https://430s2axyj6.execute-api.us-east-1.amazonaws.com/API/Appointments", {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify({ body: JSON.stringify(payload) })
      })
        .then(res => res.json())
        .then(() => {
          alert("Appointment booked!");
          this.name = "";
          this.symptoms = "";
          this.selectedSlot = "";
        })
        .catch(err => {
          console.error("Error booking appointment:", err);
          alert("Failed to book appointment.");
        });
    }
  }
};
</script>
