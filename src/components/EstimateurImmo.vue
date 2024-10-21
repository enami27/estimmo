<template>
<div class="outer-container">
    <div class="main-container">
        <img src="../assets/logo.png" class="logo" alt="logo"/>
        <h1>Futur propriétaire, agent immobilier ou potentiel vendeur? Estimez votre bien immobilier dès maintenant</h1>
      <div class="form-container">
        <div class="flex-row">
          <div class="form-group">
            <label>Type de bien</label>
            <select v-model="propertyType">
              <option value="appartement">Appartement</option>
              <option value="maison">Maison</option>
            </select>
          </div>
  
          <div class="form-group">
            <label>Commune</label>
            <select v-model="city">
              <option value="">Sélectionner une commune</option>
              <option v-for="cityName in Object.keys(donnesImmo)" 
                      :key="cityName" 
                      :value="cityName">
                {{ cityName }}
              </option>
            </select>
          </div>
  
          <div class="form-group">
            <label>Surface (m²)</label>
            <input
              type="number"
              min="0"
              v-model="surface"
              placeholder="Ex: 75"
            />
          </div>
        </div>
  
        <div class="flex-row">
          <div class="form-group">
            <label>Nombre de pièces</label>
            <input
              type="number"
              min="1"
              v-model="rooms"
              placeholder="Ex: 3"
            />
          </div>
  
          <div class="form-group">
            <label>Nombre de salles de bain</label>
            <input
              type="number"
              min="1"
              v-model="bathrooms"
              placeholder="Ex: 1"
            />
          </div>
  
          <div class="form-group">
            <label>Âge du bien (années)</label>
            <input
              type="number"
              min="0"
              v-model="propertyAge"
              placeholder="Ex: 15"
            />
          </div>
        </div>
  
        <button @click="calculatePrice">
          Estimer
        </button>
  
        <div v-if="estimatedPrice" class="resultat-prix">
          <h3>Prix estimé</h3>
          <p>{{ formatPrice(estimatedPrice) }} €</p>
        </div>
      </div>
    </div>
    </div>
  </template>

<script>
const donnesImmo = {
  "Bordeaux": { appartement: 4384, maison: 5218 },
  "Mérignac": { appartement: 3533, maison: 3984 },
  "Pessac": { appartement: 3223, maison: 2932 },
  "Talence": { appartement: 3581, maison: 4925 },
  "Bègles": { appartement: 3132, maison: 4375 },
  "Floirac": { appartement: 2910, maison: 3244 },
  "Cenon": { appartement: 2662, maison: 3529 },
  "Lormont": { appartement: 2261, maison: 3145 },
  "Arcachon": { appartement: 8142, maison: 9540 },
  "La Teste-de-Buch": { appartement: 5193, maison: 7308 },
  "Gujan-Mestras": { appartement: 4270, maison: 5225 },
  "Le Teich": { appartement: 4056, maison: 4431 },
  "Andernos-les-bains": { appartement: 4092, maison: 4682 },
  "Libourne": { appartement: 2343, maison: 2818 },
  "Saint-Émilion": { appartement: 2462, maison: 3235 },
  "Pauillac": { appartement: 1493, maison: 1827 },
  "Saint-Estèphe": { appartement: 1747, maison: 1747 },
  "Margaux": { appartement: 2148, maison: 2663 },
  "Lesparre-Médoc": { appartement: 1713, maison: 1913 },
  "Saint-Laurent-Médoc": { appartement: 1923, maison: 2247 },
  "Créon": { appartement: 2611, maison: 2743 },
  "Cadillac": { appartement: 2302, maison: 2194 },
  "Langon": { appartement: 1960, maison: 2326 },
  "La Réole": { appartement: 1517, maison: 1565 },
  "Bazas": { appartement: 1898, maison: 2087 },
};

export default {
  name: 'EstimateurImmo',
  data() {
    return {
      donnesImmo,
      surface: '',
      rooms: '',
      bathrooms: '',
      propertyType: 'appartement',
      city: '',
      propertyAge: '',
      estimatedPrice: null
    }
  },
  methods: {
    calculatePrice() {
    // verification des champs obligatoires
      if (!this.surface || !this.city || !this.propertyType) {
        alert('Veuillez remplir tous les champs obligatoires');
        return;
      }

      // contrainte : le nombre de salles de bains doit être inférieur au nombre de pièces
      if (this.bathrooms >= this.rooms) {
      alert('Le nombre de salles de bains doit être inférieur au nombre de pièces.');
      return;
    }
    
     // prix au m2 pour la ville et le type de bien sélectionnés
      const pricePerM2 = this.donnesImmo[this.city][this.propertyType];
      const basePrice = pricePerM2 * parseFloat(this.surface);
      
      // Facteur basé sur le nombre de pièces
      let roomFactor = 1;
      if (this.rooms) {
        const roomsNum = parseInt(this.rooms);
        if (roomsNum <= 2) roomFactor = 0.95;
        else if (roomsNum >= 5) roomFactor = 1.05;
      }

      // Facteur basé sur l'ancienneté
      let ageFactor = 1;
      if (this.propertyAge) {
        const age = parseInt(this.propertyAge);
        if (age <= 5) ageFactor = 1.2;  // Bien récent : bonus de 20%
        else if (age <= 10) ageFactor = 1.1;  
        else if (age <= 20) ageFactor = 1;  // Bien d'âge moyen : pas d'ajustement
        else if (age <= 50) ageFactor = 0.9;  // Bien ancien : malus de 10%
        else ageFactor = 0.8;  // Bien très ancien : malus de 20%
      }

      // Facteur basé sur le nombre de salles de bain
      let bathroomFactor = 1;
      if (this.bathrooms) {
        const bathroomsNum = parseInt(this.bathrooms);
        if (bathroomsNum === 1) bathroomFactor = 1;
        else if (bathroomsNum === 2) bathroomFactor = 1.05;  // Bonus de 5% pour 2 SDB
        else if (bathroomsNum >= 3) bathroomFactor = 1.1;  // Bonus de 10% pour 3 SDB ou plus
      }
      
      // calcul du prix estimé
      const finalPrice = basePrice * roomFactor * ageFactor * bathroomFactor;
      this.estimatedPrice = Math.round(finalPrice);
    },
    formatPrice(price) {
      return price.toLocaleString();
    }
  }
}
</script>

<style scoped>

.resultat-prix {
    text-align: center;
}

.resultat-prix h3 {
    font-size: 26px;
    font-weight: 500;
    margin-bottom: 5px; 
}

.resultat-prix p {
    font-size: 48px; 
    font-weight: 700;
    margin-top: 5px;
}

.outer-container {
  display: flex;
  justify-content: center;
  align-items: center; 
  height: 100vh;
}


.logo {
    width: 500px;
    height: auto;
}
.main-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding-right: 70px;
  padding-left: 70px;
  gap: 20px;
}
.form-container {
  display: flex;
  flex-direction: column;
  gap: 20px;
}

.flex-row {
  display: flex;
  justify-content: space-evenly;
  margin-bottom: 15px;
}

.form-group {
  display: flex;
  flex-direction: row;
  margin-right: 10px;
  gap: 10px;
  justify-content: center;
  align-items: center;
}

button {
  width: auto;
  padding: 15px 30px;
  margin: 0 auto;
  display: block;
  background-color: #330E0E; 
  color: #E0D9CB; 
  border: none; 
  border-radius: 40px; 
  font-size: 20px; 
  cursor: pointer; 
  transition: 0.4s;
  font-weight: 700;

}

button:hover {
  background-color: #621b1b; 
}

.form-group input,
.form-group select {
    border: 2px solid #330E0E; 
    border-radius: 30px; 
    background-color: #E0D9CB; 
    padding: 10px; 
    font-size: 16px; 
    color: #330E0E; 
    opacity: 0.6;

}

.form-group input::placeholder,
.form-group select::placeholder {
    color: #330E0E;
    opacity: 0.6;
}

.form-group select option {
    color: #330E0E;
}

.form-group input:focus,
.form-group select:focus {
    outline: none; 
    box-shadow: none; 
}


</style>