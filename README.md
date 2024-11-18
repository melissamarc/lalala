import React, { useState, useEffect } from 'react';
import Sidebar from '../../components/sidebar/sidebar';
import NutritionalChart from '../../components/NutritionChart/NutritionChart';
import './dashboard.css';

interface NutritionalValues {
  totalCalories: number;
  totalProtein: number;
  totalVitamins: number;
  totalCarbohydrates: number;
}

const Dashboard: React.FC = () => {
  const [meals, setMeals] = useState<NutritionalValues[]>([]);
  const [waterConsumed, setWaterConsumed] = useState<number>(0);

  useEffect(() => {
    const storedMeals = localStorage.getItem('meals');
    const storedTimestamp = localStorage.getItem('mealsTimestamp');
    const currentTime = new Date().getTime();

    if (storedMeals && storedTimestamp) {
      const parsedMeals = JSON.parse(storedMeals);
      const timestamp = JSON.parse(storedTimestamp);

      if (currentTime - timestamp < 24 * 60 * 60 * 1000) {
        setMeals(parsedMeals);
      } else {
        localStorage.removeItem('meals');
        localStorage.removeItem('mealsTimestamp');
      }
    }
  }, []);

  const combinedNutritionalValues = meals.reduce(
    (acc, meal) => ({
      totalCalories: acc.totalCalories + meal.totalCalories,
      totalProtein: acc.totalProtein + meal.totalProtein,
      totalVitamins: acc.totalVitamins + meal.totalVitamins,
      totalCarbohydrates: acc.totalCarbohydrates + meal.totalCarbohydrates,
    }),
    {
      totalCalories: 0,
      totalProtein: 0,
      totalVitamins: 0,
      totalCarbohydrates: 0,
    }
  );

  const handleAddWater = () => {
    if (waterConsumed < 3000) {
      setWaterConsumed(waterConsumed + 250);
    }
  };

  const handleReduceWater = () => {
    if (waterConsumed > 0) {
      setWaterConsumed(waterConsumed - 250);
    }
  };

  const handleResetWater = () => {
    setWaterConsumed(0);
  };

  return (
    <div className="dashboard-container">
      <Sidebar />
      <div className="charts-container">
        <div className="progress-bars">
          <div className="progress-bar">
            <span>Calorias: {combinedNutritionalValues.totalCalories} kcal</span>
            <div className="progress">
              <div
                className="progress-bar-fill"
                style={{
                  width: Math.min(combinedNutritionalValues.totalCalories, 500) + 'px', // Corrigido
                }}
              ></div>
            </div>
          </div>
          <div className="progress-bar">
            <span>Proteínas: {combinedNutritionalValues.totalProtein} g</span>
            <div className="progress">
              <div
                className="progress-bar-fill"
                style={{
                  width: Math.min(combinedNutritionalValues.totalProtein, 500) + 'px', // Corrigido
                }}
              ></div>
            </div>
          </div>
          <div className="progress-bar">
            <span>Vitaminas: {combinedNutritionalValues.totalVitamins}</span>
            <div className="progress">
              <div
                className="progress-bar-fill"
                style={{
                  width: Math.min(combinedNutritionalValues.totalVitamins, 500) + 'px', // Corrigido
                }}
              ></div>
            </div>
          </div>
          <div className="progress-bar">
            <span>Carboidratos: {combinedNutritionalValues.totalCarbohydrates}</span>
            <div className="progress">
              <div
                className="progress-bar-fill"
                style={{
                  width: Math.min(combinedNutritionalValues.totalCarbohydrates, 500) + 'px', // Corrigido
                }}
              ></div>
            </div>
          </div>
        </div>

        <NutritionalChart nutritionalValues={combinedNutritionalValues} />

        <div className="water-counter">
          <span className="water-label">Água Consumida: {waterConsumed} ml</span>
          <div className="water-cup">
            <div
              className="water-level"
              style={{
                height: Math.min((waterConsumed / 3000) * 100, 100) + '%', // Corrigido
              }}
            ></div>
            <span className="water-percentage">
              {Math.min((waterConsumed / 3000) * 100, 100).toFixed(0)}%
            </span>
            <span className="water-drop-icon">💧</span>
          </div>

          <div className="water-controls">
            <button className="water-button" onClick={handleAddWater}>+</button>
            <button className="water-button" onClick={handleReduceWater}>-</button>
            <button className="reset-water-button" onClick={handleResetWater}>Esvaziar Copo</button>
          </div>
        </div>
      </div>
    </div>
  );
};

export default Dashboard;



/* Estilização do contador de água */
.water-counter {
  text-align: center;
  margin-top: 20px;
}

.water-label {
  font-size: 18px;
  font-weight: bold;
  margin-bottom: 10px;
  display: block;
}

/* Container do copo de água */
.water-cup {
  position: relative;
  width: 100px;
  height: 150px;
  border: 2px solid #0288d1; /* Borda para simular o copo */
  border-radius: 10px; /* Deixa os cantos arredondados */
  overflow: hidden;
  margin: 0 auto;
  background: rgba(3, 169, 244, 0.1); /* Fundo claro simulando vidro */
}

/* Nível da água dentro do copo */
.water-level {
  position: absolute;
  bottom: 0;
  left: 0;
  width: 100%;
  background: rgba(3, 169, 244, 0.7); /* Azul claro representando a água */
  transition: height 0.3s ease-in-out; /* Suaviza a transição do nível da água */
  z-index: 1;
}

/* Porcentagem da água consumida */
.water-percentage {
  position: absolute;
  bottom: 5px;
  width: 100%;
  text-align: center;
  font-size: 14px;
  color: white;
  font-weight: bold;
  z-index: 2;
}

/* Ícone de gota de água */
.water-drop-icon {
  font-size: 24px;
  color: #0288d1;
  margin-top: 5px;
}

/* Controles de água (botões) */
.water-controls {
  display: flex;
  justify-content: center;
  gap: 10px;
  margin-top: 15px;
}

.water-button,
.reset-water-button {
  padding: 10px 15px;
  font-size: 16px;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  color: white;
  font-weight: bold;
  transition: background-color 0.3s ease;
}

.water-button {
  background-color: #4fc3f7; /* Azul claro */
}

.water-button:hover {
  background-color: #0288d1; /* Azul mais escuro */
}

.reset-water-button {
  background-color: #e57373; /* Vermelho claro */
}

.reset-water-button:hover {
  background-color: #c62828; /* Vermelho mais escuro */
}

/* Estilo geral das barras de progresso */
.progress-bars {
  margin: 20px 0;
}

.progress-bar {
  margin-bottom: 15px;
}

.progress-bar span {
  font-size: 16px;
  font-weight: bold;
  display: block;
  margin-bottom: 5px;
}

.progress {
  width: 100%;
  background-color: #e0e0e0; /* Fundo da barra */
  border-radius: 5px;
  height: 20px;
  overflow: hidden;
}

.progress-bar-fill {
  height: 100%;
  background-color: #4caf50; /* Cor da barra preenchida */
  border-radius: 5px;
  transition: width 0.3s ease-in-out; /* Suaviza o preenchimento */
}


