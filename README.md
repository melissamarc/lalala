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
  const [waterConsumed, setWaterConsumed] = useState<number>(0); // Contador de água

  useEffect(() => {
    // Recuperando as refeições armazenadas no localStorage
    const storedMeals = localStorage.getItem('meals');
    const storedTimestamp = localStorage.getItem('mealsTimestamp');
    const currentTime = new Date().getTime();

    if (storedMeals && storedTimestamp) {
      const parsedMeals = JSON.parse(storedMeals);
      const timestamp = JSON.parse(storedTimestamp);

      // Verifica se os dados são válidos (não expiraram após 24 horas)
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
    if (waterConsumed < 3000) { // Limita o consumo de água a 3L
      setWaterConsumed(waterConsumed + 250); // A cada vez que o usuário clicar, adiciona 250ml
    }
  };

  const handleReduceWater = () => {
    if (waterConsumed > 0) { // Evita reduzir a água abaixo de 0
      setWaterConsumed(waterConsumed - 250); // Reduz a quantidade de água em 250ml
    }
  };

  const handleResetWater = () => {
    setWaterConsumed(0); // Resetando o contador de água
  };

  return (
    <div className="dashboard-container">
      <Sidebar /> {/* Incluindo o Sidebar aqui, caso precise */}
      <div className="charts-container">
        <div className="progress-bars">
          <div className="progress-bar">
            <span>Calorias: {combinedNutritionalValues.totalCalories} kcal</span>
            <div className="progress">
              <div
                className="progress-bar-fill"
                style={{
                  width: `${Math.min(combinedNutritionalValues.totalCalories, 500)}px`, // Limitar a largura
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
                  width: `${Math.min(combinedNutritionalValues.totalProtein, 500)}px`,
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
                  width: `${Math.min(combinedNutritionalValues.totalVitamins, 500)}px`,
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
                  width: `${Math.min(combinedNutritionalValues.totalCarbohydrates, 500)}px`,
                }}
              ></div>
            </div>
          </div>
        </div>

        {/* Exibindo o gráfico nutricional */}
        <NutritionalChart nutritionalValues={combinedNutritionalValues} />

        {/* Contador de água com formato de copo */}
        <div className="water-counter">
          <span className="water-label">Água Consumida: {waterConsumed} ml</span>
          <div className="water-cup">
            <div
              className="water-level"
              style={{
                height: `${Math.min((waterConsumed / 3000) * 100, 100)}%`, // Controla a altura do nível da água
              }}
            ></div>
            <span className="water-percentage">{Math.min((waterConsumed / 3000) * 100, 100).toFixed(0)}%</span>
          </div>
          
          {/* Botões de adição e redução de água */}
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


Css

/* Layout geral da dashboard */
.dashboard-container {
  display: flex;
  flex-direction: row;
  height: 100vh;
}

/* Estilo do Sidebar */
.sidebardesh {
  width: 250px;
  background-color: #2d2d2d;
  color: white;
  padding: 20px;
  position: fixed;
  top: 0;
  left: 0;
  height: 100%;
}

/* Estilo dos gráficos e progress bars */
.charts-container {
  margin-left: 250px;
  padding: 20px;
  display: flex;
  flex-direction: column;
  align-items: center;
  width: 100%;
  height: 100%;
  overflow-y: auto;
}

/* Estilos para as barras de progresso */
.progress-bars {
  width: 80%;
  max-width: 600px;
  margin-bottom: 40px;
}

/* Estilo do contador de água */
.water-counter {
  width: 80%;
  max-width: 300px;
  margin-top: 40px;
  text-align: center;
  position: relative;
}

/* Formato do copo de água */
.water-cup {
  width: 100%;
  height: 250px;
  background-color: #e0f7fa;
  border-radius: 10px;
  position: relative;
  border: 2px solid #00796b;
}

.water-level {
  width: 100%;
  background-color: #70b8d9;
  border-radius: 10px 10px 0 0;
  position: absolute;
  bottom: 0;
  transition: height 0.3s ease-in-out;
}

.water-percentage {
  position: absolute;
  top: 10px;
  left: 50%;
  transform: translateX(-50%);
  font-size: 16px;
  font-weight: bold;
  color: #00796b;
}

/* Botões de controle */
.water-controls {
  display: flex;
  justify-content: space-between;
  margin-top: 20px;
}

.water-button {
  width: 40px;
  height: 40px;
  font-size: 20px;
  color: white;
  background-color: #0eba0e;
  border: none;
  border-radius: 50%;
  cursor: pointer;
}

.water-button:hover {
  background-color: #004d40;
}

.reset-water-button {
  margin-top: 20px;
  padding: 10px 20px;
  background-color: hwb(140 36% 19%);
  color: white;
  border: none;
  border-radius: 8px;
}



