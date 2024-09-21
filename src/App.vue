<template>
  <div class="min-h-screen bg-gradient-to-r from-blue-500 to-teal-500 p-8">
    <!-- Header with Logo and Random Message -->
    <header class="bg-white text-blue-600 p-4 mb-6 rounded-lg shadow-lg flex justify-between items-center">
      <div class="text-3xl font-extrabold tracking-tight">Stem</div>
      <div class="italic text-xl" v-text="randomMessage"></div>
    </header>

    <!-- Form Section -->
    <div class="bg-white p-8 rounded-lg shadow-lg max-w-4xl mx-auto">
      <!-- Input Name -->
      <div class="mb-6">
        <label for="name" class="block text-lg font-medium text-gray-700">Enter your Name:</label>
        <input v-model="name" type="text" id="name" class="mt-2 block w-full border border-gray-300 rounded-lg shadow-sm p-3 text-lg focus:outline-none focus:ring-2 focus:ring-blue-500" placeholder="Your Name" />
      </div>

      <!-- Select Month -->
      <div class="mb-6">
        <label for="month" class="block text-lg font-medium text-gray-700">Select Month:</label>
        <select v-model="selectedMonth" id="month" class="mt-2 block w-full border border-gray-300 rounded-lg shadow-sm p-3 text-lg focus:outline-none focus:ring-2 focus:ring-blue-500">
          <option value="" disabled selected>Select a month</option>
          <option v-for="month in months" :key="month" :value="month">{{ month }}</option>
        </select>
      </div>

      <!-- Select Class -->
      <div class="mb-6">
        <label for="class" class="block text-lg font-medium text-gray-700">Select Class:</label>
        <select v-model="selectedClass" id="class" class="mt-2 block w-full border border-gray-300 rounded-lg shadow-sm p-3 text-lg focus:outline-none focus:ring-2 focus:ring-blue-500">
          <option value="" disabled selected>Select a class</option>
          <option v-for="classOption in classes" :key="classOption" :value="classOption">{{ classOption }}</option>
        </select>
      </div>

      <!-- Input for Number of Subjects -->
      <div class="mb-6">
        <label for="numSubjects" class="block text-lg font-medium text-gray-700">Number of Subjects:</label>
        <input v-model="numSubjects" type="number" id="numSubjects" class="mt-2 block w-full border border-gray-300 rounded-lg shadow-sm p-3 text-lg focus:outline-none focus:ring-2 focus:ring-blue-500" />
      </div>

      <!-- Table for Marks Entry -->
      <div v-if="numSubjects > 0" class="mt-8">
        <table class="min-w-full bg-white border border-gray-200 shadow-md rounded-lg overflow-hidden">
          <thead class="bg-gray-50 text-gray-600">
            <tr>
              <th class="border px-4 py-2 text-left font-semibold text-lg">Subject</th>
              <th class="border px-4 py-2 text-left font-semibold text-lg">Total Marks</th>
              <th class="border px-4 py-2 text-left font-semibold text-lg">Obtained Marks</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="n in numSubjects" :key="n" class="hover:bg-gray-50">
              <td class="border px-4 py-3">
                <input v-model="subjects[n-1]" type="text" class="block w-full border border-gray-300 rounded-md shadow-sm p-2" placeholder="Enter subject">
              </td>
              <td class="border px-4 py-3">
                <input v-model="totalMarks[n-1]" type="number" class="block w-full border border-gray-300 rounded-md shadow-sm p-2" placeholder="Total Marks">
              </td>
              <td class="border px-4 py-3">
                <input v-model="obtainedMarks[n-1]" type="number" class="block w-full border border-gray-300 rounded-md shadow-sm p-2" placeholder="Obtained Marks">
              </td>
            </tr>
          </tbody>
        </table>
      </div>

      <!-- Display Total Marks -->
      <div v-if="numSubjects > 0" class="mt-6 text-lg font-bold text-gray-700">
        Total Marks: {{ tm }}
      </div>

      <!-- Display Obtained Marks-->
       <div v-if="numSubjects > 0" class="mt-6 text-lg font-bold text-gray-700">
        Obtained Marks: {{ ob }}
       </div>

      <!-- Generate PDF Button -->
      <div class="mt-8 flex justify-end">
        <button @click="generatePDF" class="bg-blue-600 text-white px-6 py-3 rounded-lg text-lg font-bold hover:bg-blue-700 transition duration-300">
          Generate PDF
        </button>
      </div>
    </div>
  </div>
</template>

<script>
import jsPDF from 'jspdf';
import 'jspdf-autotable';
import logo from '@/assets/STEM.jpg';

export default {
  data() {
    return {
      name: '',
      selectedMonth: '',
      selectedClass: '',
      numSubjects: 0,
      subjects: [],
      totalMarks: [],
      obtainedMarks: [],
      randomMessage: this.getRandomMessage(),
      months: ['January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 'October', 'November', 'December'],
      classes: ['Matric', 'Intermediate', 'Bachelor', 'Master', 'PhD'],
    };
  },
  methods: {
    getRandomMessage() {
      const messages = [
        "20-Hammad Colony Shadbagh Lahore, Phone: 03227602299"
      ];
      return messages[Math.floor(Math.random() * messages.length)];
    },
    getRandomRemark(percentage) {
    if (percentage >= 85) return "Excellent work! Keep it up!";
    if (percentage >= 75 && percentage < 85) return "Great work! Keep it up!";
    if (percentage >= 50 && percentage < 75) return "You need more hard work!";
    return "Fail!";
  },
  generatePDF() {
  const doc = new jsPDF({
    orientation: 'landscape'
  });

  const img = new Image();
  img.src = logo;

  img.onload = () => {
    // Add the logo to the PDF (Position it at (10, 10) and adjust width/height)
    doc.addImage(img, 'JPEG', 10, 10, 25, 25);  // (x, y, width, height)

    // Centered Academy Header (Academy Name and Address)
    doc.setFontSize(18);
    doc.setFont('helvetica', 'bold');
    doc.text('STEM ACADEMY', 140, 20, { align: 'center' });  // Centered on the page

    // Academy Address Centered
    doc.setFontSize(12);
    doc.setFont('helvetica', 'normal');
    doc.text('20-Hammad Colony Shadbagh Lahore, Phone: 03227602299', 140, 28, { align: 'center' });  // Centered on the page

    // Centered Student Information Section
    doc.setFontSize(12);
    doc.text(`STUDENT NAME: ${this.name}`, 140, 50, { align: 'center' });  // Centered on the page
    doc.text(`STUDENT CLASS: ${this.selectedClass}`, 10, 58);  // Left aligned
    doc.text(`FOR THE MONTH OF ${this.selectedMonth}`, 140, 58, { align: 'center' });  // Centered on the page

    // Table for Subjects, Total Marks, and Obtained Marks
    const headers = [["SUBJECTS", "TOTAL MARKS", "OBTAINED MARKS"]];
    const data = this.subjects.map((subject, index) => [
      subject, this.totalMarks[index], this.obtainedMarks[index]
    ]);

    // Add Total row to the table data
    data.push(["TOTAL", this.tm, this.ob]);

    doc.autoTable({
      startY: 70,  // Adjust start position
      head: headers,
      body: data,
      theme: 'grid',
      styles: { halign: 'center' },  // Center align text
      margin: { top: 10 }
    });

    // Calculate Percentage
    const percentage = ((this.ob / this.tm) * 100).toFixed(2);

    // Display Percentage below the table (Centered)
    const finalY = doc.lastAutoTable.finalY + 10;
    doc.text(`PERCENTAGE: ${percentage}%`, 140, finalY, { align: 'center' });  // Centered on the page

    // Add Random Remark Based on Percentage (Left aligned)
    const remark = this.getRandomRemark(percentage);
    doc.text(`Remark: ${remark}`, 10, finalY + 10);

    // Save the PDF
    doc.save(`${this.name}.pdf`);
  };

  img.onerror = (error) => {
    console.error("Error loading logo image", error);
  };
}
},

  watch: {
    numSubjects(newVal) {
      this.subjects = Array(parseInt(newVal)).fill('');
      this.totalMarks = Array(parseInt(newVal)).fill('');
      this.obtainedMarks = Array(parseInt(newVal)).fill('');
    }
  },

  computed: {
  tm() {
    return this.totalMarks.reduce((marks, item) => {
      const num = Number(item);
      return marks + (isNaN(num) ? 0 : num);
    }, 0);
  },
  ob() {
    return this.obtainedMarks.reduce((marks, item) => {
      const num = Number(item);
      return marks + (isNaN(num) ? 0 : num);
    }, 0);
  }
}

};
</script>