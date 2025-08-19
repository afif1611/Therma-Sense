//Energy efficient thermostat control system

document.addEventListener('DOMContentLoaded', function() {



    // --- Calculator Script ---
    (function setupInteractiveCalculator() {
        let outsideTempValue = 28;
        let insideTempValue = 24;
        let hoursUsageValue = 8;
        const tempMin = 10, tempMax = 40;
        const insideTempMin = 16, insideTempMax = 30;
        const hoursMin = 1, hoursMax = 24;
        const FIXED_ELECTRICITY_RATE_CENTS = 24.43; 
        const EFFICIENT_COOLING_TEMP = 24; 
        const COST_AT_EFFICIENT_COOLING_8H = 1.36; // Assume 0.7kWh per hour
        const INEFFICIENT_COOLING_TEMP = 21; 
        const COST_AT_INEFFICIENT_COOLING_8H = 2.15; // Assume 1.1kWh per hour
        const COST_PER_DEGREE_8H_COOLING = (COST_AT_INEFFICIENT_COOLING_8H - COST_AT_EFFICIENT_COOLING_8H) / (EFFICIENT_COOLING_TEMP - INEFFICIENT_COOLING_TEMP);
        const EFFICIENT_HEATING_TEMP = 21; 
        const COST_AT_EFFICIENT_HEATING_8H = 1.36; 
        const INEFFICIENT_HEATING_TEMP_EXAMPLE = 23; 
        const HEATING_INEFFICIENCY_COST_FACTOR = 1.25; 
        const COST_AT_INEFFICIENT_HEATING_EXAMPLE_8H = COST_AT_EFFICIENT_HEATING_8H * HEATING_INEFFICIENCY_COST_FACTOR;
        const COST_PER_DEGREE_8H_HEATING = (COST_AT_INEFFICIENT_HEATING_EXAMPLE_8H - COST_AT_EFFICIENT_HEATING_8H) / (INEFFICIENT_HEATING_TEMP_EXAMPLE - EFFICIENT_HEATING_TEMP);
        const outsideTempValDisplay = document.getElementById('outside-temp-val');
        const insideTempValDisplay = document.getElementById('inside-temp-val');
        const hoursUsageValDisplay = document.getElementById('hours-usage-val');
        const outsideTempIncrementBtn = document.getElementById('outside-temp-increment');
        const outsideTempDecrementBtn = document.getElementById('outside-temp-decrement');
        const insideTempIncrementBtn = document.getElementById('inside-temp-increment');
        const insideTempDecrementBtn = document.getElementById('inside-temp-decrement');
        const hoursUsageIncrementBtn = document.getElementById('hours-usage-increment');
        const hoursUsageDecrementBtn = document.getElementById('hours-usage-decrement');
        const usageCostValueDisplay = document.getElementById('usage-cost-value'); 
        const kwhUsageValueDisplay = document.getElementById('kwh-usage-value');
        const savingsRecommendationCaption = document.getElementById('savings-recommendation-caption'); 
        const usageCostLabel = document.getElementById('usage-cost-label'); 
        function calculateUserCostPerDayFor8Hours(outsideTemp, insideTemp) {
            let costFor8Hours = 0;
            if (outsideTemp > insideTemp) { 
                costFor8Hours = COST_AT_EFFICIENT_COOLING_8H + (EFFICIENT_COOLING_TEMP - insideTemp) * COST_PER_DEGREE_8H_COOLING;
            } else if (outsideTemp < insideTemp) { 
                costFor8Hours = COST_AT_EFFICIENT_HEATING_8H + (insideTemp - EFFICIENT_HEATING_TEMP) * COST_PER_DEGREE_8H_HEATING;
            }
            return Math.max(0.01, costFor8Hours); 
        }
        
        function getRecommendationText(outsideTemp, insideTemp) { 
            const dailyWastedCoolingAt21C = COST_AT_INEFFICIENT_COOLING_8H - COST_AT_EFFICIENT_COOLING_8H; 
            const monthlyWastedCoolingAt21C = (dailyWastedCoolingAt21C * 30).toFixed(2);
            const dailyExtraCostHeatingVsEfficient = COST_AT_INEFFICIENT_HEATING_EXAMPLE_8H - COST_AT_EFFICIENT_HEATING_8H; 
            const monthlyExtraCostHeatingVsEfficient = (dailyExtraCostHeatingVsEfficient * 30).toFixed(2);
            let message = ""; let isInefficient = false;
            if (outsideTemp > insideTemp) { 
                if (insideTemp >= 24 && insideTemp <= 26) { message = `You’re using an efficient cooling setting — great job! If you had chosen 21°C, you’d be spending extra RM${dailyWastedCoolingAt21C.toFixed(2)} per day.`;
                } else if (insideTemp === 21) { message = `Try increasing your AC temp to 24–26°C to save energy. Your current setting could be costing you about RM${dailyWastedCoolingAt21C.toFixed(2)} extra per day, or over RM${Math.floor(dailyWastedCoolingAt21C * 30)}/month.`; isInefficient = true;
                } else if (insideTemp < 21) { const userCost8h = calculateUserCostPerDayFor8Hours(outsideTemp, insideTemp); const extraCostComparedTo21C = userCost8h - COST_AT_INEFFICIENT_COOLING_8H; const totalExtraCost = dailyWastedCoolingAt21C + Math.max(0, extraCostComparedTo21C); message = `This is a very cool setting. Increasing to 24-26°C could save you around RM${totalExtraCost.toFixed(2)} per day.`; isInefficient = true;
                } else { message = "Cooling temp is high — if still comfortable, you’re saving even more energy!";}
            } else if (outsideTemp < insideTemp) { 
                if (insideTemp >= 20 && insideTemp <= 21) { message = `You're using an efficient heating setting. Great job! Compared to a less efficient ${INEFFICIENT_HEATING_TEMP_EXAMPLE}°C, you're saving about RM${dailyExtraCostHeatingVsEfficient.toFixed(2)} per day.`;
                } else if (insideTemp > 21) { const userCost8h = calculateUserCostPerDayFor8Hours(outsideTemp, insideTemp); const extraCostComparedToEfficient = userCost8h - COST_AT_EFFICIENT_HEATING_8H; message = `Try lowering your heating temp to 20–21°C for better efficiency. Your current setting might be costing you an extra ~RM${extraCostComparedToEfficient.toFixed(2)} per day, or over RM${(extraCostComparedToEfficient*30).toFixed(0)}/month.`; isInefficient = true;
                } else { message = "This heating setting is quite low. If comfortable, you're saving a good amount of energy!";}
            } else { message = "Inside temp is close to outside — AC may not be needed.";}
            return { text: message, inefficient: isInefficient };
        }
        function updateCalculatorDisplayAndInfo() { 
            outsideTempValDisplay.textContent = outsideTempValue; insideTempValDisplay.textContent = insideTempValue; hoursUsageValDisplay.textContent = hoursUsageValue;
            const electricityRateInRM = FIXED_ELECTRICITY_RATE_CENTS / 100; 
            const costFor8Hours = calculateUserCostPerDayFor8Hours(outsideTempValue, insideTempValue);
            const estimatedUsageCost = (costFor8Hours / 8) * hoursUsageValue; 
            let estimatedKwh = 0; if (electricityRateInRM > 0 && estimatedUsageCost > 0) { estimatedKwh = estimatedUsageCost / electricityRateInRM;}
            usageCostLabel.textContent = "Estimated Usage Cost*"; usageCostValueDisplay.textContent = `RM${estimatedUsageCost.toFixed(2)}`; kwhUsageValueDisplay.textContent = `${estimatedKwh.toFixed(1)} KWH`;
            const recommendationInfo = getRecommendationText(outsideTempValue, insideTempValue);
            savingsRecommendationCaption.textContent = recommendationInfo.text;
            if (recommendationInfo.inefficient) { savingsRecommendationCaption.classList.add('recommendation-inefficient'); } else { savingsRecommendationCaption.classList.remove('recommendation-inefficient');}
        }
        outsideTempIncrementBtn.addEventListener('click', () => { if (outsideTempValue < tempMax) { outsideTempValue++; updateCalculatorDisplayAndInfo(); }});
        outsideTempDecrementBtn.addEventListener('click', () => { if (outsideTempValue > tempMin) { outsideTempValue--; updateCalculatorDisplayAndInfo(); }});
        insideTempIncrementBtn.addEventListener('click', () => { if (insideTempValue < insideTempMax) { insideTempValue++; updateCalculatorDisplayAndInfo(); }});
        insideTempDecrementBtn.addEventListener('click', () => { if (insideTempValue > insideTempMin) { insideTempValue--; updateCalculatorDisplayAndInfo(); }});
        hoursUsageIncrementBtn.addEventListener('click', () => { if (hoursUsageValue < hoursMax) { hoursUsageValue++; updateCalculatorDisplayAndInfo(); }});
        hoursUsageDecrementBtn.addEventListener('click', () => { if (hoursUsageValue > hoursMin) { hoursUsageValue--; updateCalculatorDisplayAndInfo(); }});
        updateCalculatorDisplayAndInfo(); 
    })(); 
    
});
