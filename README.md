.dashboard-container {
  display: flex;
  justify-content: space-between;
  padding: 20px;
}

.dashboard-content {
  width: 100%;
  padding: 20px;
}

h2 {
  margin-bottom: 20px;
  font-size: 2rem;
  color: #333;
}

.charts-container {
  margin-top: 20px;
}

.progress-bars {
  margin-bottom: 20px;
}

.progress-bar {
  margin-bottom: 10px;
}

.progress {
  background-color: #f3f3f3;
  border-radius: 20px;
  height: 20px;
}

.progress-bar-fill {
  background-color: #66b3ff;
  height: 100%;
  border-radius: 20px;
}

.progress-bar span {
  font-size: 1rem;
  margin-right: 10px;
}


import React, { useState, useEffect } from 'react';
import Sidebar from '../../components/sidebar/sidebar';
import NutritionalChart from '../../components/NutritionChart/NutritionChart'; // Suponho que você tenha esse componente de gráfico
import './dashboard.css';

interface NutritionalValues {
  totalCalories: number;
  totalProtein: number;
  totalVitamins: number;
  totalCarbohydrates: number;
}

const Dashboard: React.FC = () => {
  const [meals, setMeals] = useState<NutritionalValues[]>([]);

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
    { totalCalories: 0, totalProtein: 0, totalVitamins: 0, totalCarbohydrates: 0 }
  );

  return (
    <>
      <Sidebar />
      <div className="dashboard-container1">
        <div className="dashboard-content1">
          <h2>Resumo Nutricional das Refeições</h2>

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

            <NutritionalChart nutritionalValues={combinedNutritionalValues} />
          </div>
        </div>
      </div>
    </>
  );
};

export default Dashboard;



