<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Dynamic Subscription Plan Selector</title>
    <style>
        :root {
            --primary: #4F46E5;
            --primary-dark: #4338ca;
            --secondary: #64748b;
            --bg-page: #F3F4F6;
            --bg-card: #ffffff;
            --text-main: #111827;
            --text-muted: #6b7280;
            --success: #10b981;
            --danger: #ef4444;
            --radius: 16px;
            --shadow-sm: 0 1px 2px 0 rgba(0, 0, 0, 0.05);
            --shadow-md: 0 4px 6px -1px rgba(0, 0, 0, 0.1), 0 2px 4px -1px rgba(0, 0, 0, 0.06);
            --shadow-lg: 0 10px 15px -3px rgba(0, 0, 0, 0.1), 0 4px 6px -2px rgba(0, 0, 0, 0.05);
            --disabled-opacity: 0.6;
        }

        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            font-family: 'Segoe UI', system-ui, -apple-system, sans-serif;
        }

        body {
            background-color: var(--bg-page);
            color: var(--text-main);
            min-height: 100vh;
            display: flex;
            flex-direction: column;
            align-items: center;
            padding: 40px 20px;
        }

        /* --- Header & Controls --- */
        header {
            text-align: center;
            max-width: 800px;
            margin-bottom: 50px;
        }

        h1 {
            font-size: 2.5rem;
            font-weight: 800;
            margin-bottom: 15px;
            color: var(--text-main);
        }

        p.subtitle {
            color: var(--text-muted);
            font-size: 1.1rem;
            margin-bottom: 40px;
        }

        .controls-panel {
            background: var(--bg-card);
            padding: 30px;
            border-radius: var(--radius);
            box-shadow: var(--shadow-md);
            width: 100%;
            max-width: 600px;
            margin: 0 auto;
        }

        /* Slider Styling */
        .slider-container {
            margin-bottom: 30px;
        }

        .slider-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 15px;
        }

        .slider-label {
            font-weight: 600;
            font-size: 1.1rem;
        }

        .slider-value {
            font-size: 1.5rem;
            font-weight: 800;
            color: var(--primary);
            background: #e0e7ff;
            padding: 4px 12px;
            border-radius: 8px;
        }

        input[type="range"] {
            -webkit-appearance: none;
            width: 100%;
            height: 8px;
            background: #e5e7eb;
            border-radius: 4px;
            outline: none;
            cursor: pointer;
        }

        input[type="range"]::-webkit-slider-thumb {
            -webkit-appearance: none;
            width: 28px;
            height: 28px;
            background: var(--primary);
            border: 4px solid #ffffff;
            border-radius: 50%;
            cursor: pointer;
            box-shadow: 0 0 0 4px rgba(79, 70, 229, 0.2);
            transition: transform 0.1s;
        }

        input[type="range"]::-webkit-slider-thumb:hover {
            transform: scale(1.1);
        }

        /* Toggle Switch Styling */
        .billing-toggle {
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 15px;
            padding-top: 20px;
            border-top: 1px solid #f3f4f6;
        }

        .switch-label {
            font-weight: 500;
            color: var(--text-muted);
            cursor: pointer;
            transition: color 0.3s;
        }

        .switch-label.active {
            color: var(--text-main);
            font-weight: 700;
        }

        .toggle-switch {
            position: relative;
            display: inline-block;
            width: 52px;
            height: 28px;
        }

        .toggle-switch input {
            opacity: 0;
            width: 0;
            height: 0;
        }

        .slider-round {
            position: absolute;
            cursor: pointer;
            top: 0; left: 0; right: 0; bottom: 0;
            background-color: #cbd5e1;
            transition: .4s;
            border-radius: 34px;
        }

        .slider-round:before {
            position: absolute;
            content: "";
            height: 20px;
            width: 20px;
            left: 4px;
            bottom: 4px;
            background-color: white;
            transition: .4s;
            border-radius: 50%;
        }

        input:checked + .slider-round {
            background-color: var(--primary);
        }

        input:checked + .slider-round:before {
            transform: translateX(24px);
        }

        .discount-badge {
            background: #dcfce7;
            color: #166534;
            font-size: 0.75rem;
            font-weight: 700;
            padding: 4px 8px;
            border-radius: 12px;
            text-transform: uppercase;
        }

        /* --- Pricing Grid --- */
        .pricing-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
            gap: 2rem;
            width: 100%;
            max-width: 1200px;
            margin-top: 50px;
        }

        .plan-card {
            background: var(--bg-card);
            border-radius: var(--radius);
            padding: 2.5rem;
            box-shadow: var(--shadow-md);
            transition: all 0.3s ease;
            position: relative;
            border: 2px solid transparent;
            display: flex;
            flex-direction: column;
            overflow: hidden;
        }

        .plan-card:hover {
            transform: translateY(-5px);
            box-shadow: var(--shadow-lg);
        }

        .plan-card.recommended {
            border-color: var(--primary);
        }

        .plan-card.disabled {
            opacity: 0.7;
            filter: grayscale(1);
            pointer-events: none;
            background: #fafafa;
        }

        .recommended-badge {
            position: absolute;
            top: 0;
            left: 50%;
            transform: translateX(-50%);
            background: var(--primary);
            color: white;
            padding: 8px 20px;
            font-size: 0.85rem;
            font-weight: 600;
            border-bottom-left-radius: 12px;
            border-bottom-right-radius: 12px;
        }

        .plan-header {
            margin-bottom: 25px;
            text-align: center;
        }

        .plan-name {
            font-size: 1.5rem;
            font-weight: 700;
            margin-bottom: 10px;
        }

        .plan-price-wrapper {
            display: flex;
            align-items: baseline;
            justify-content: center;
            gap: 4px;
        }

        .currency {
            font-size: 1.5rem;
            font-weight: 500;
            color: var(--text-main);
        }

        .price {
            font-size: 3.5rem;
            font-weight: 800;
            color: var(--text-main);
            letter-spacing: -1px;
        }

        .period {
            color: var(--text-muted);
            font-weight: 500;
        }

        .total-summary {
            text-align: center;
            font-size: 0.9rem;
            color: var(--text-muted);
            margin-top: 5px;
            height: 20px; /* retain height to prevent jump */
        }

        .features-list {
            list-style: none;
            margin: 30px 0;
            flex-grow: 1;
        }

        .features-list li {
            padding: 12px 0;
            border-bottom: 1px solid #f3f4f6;
            color: var(--secondary);
            display: flex;
            align-items: center;
            font-size: 0.95rem;
        }

        .features-list li:last-child {
            border-bottom: none;
        }

        .features-list li svg {
            width: 20px;
            height: 20px;
            margin-right: 12px;
            color: var(--success);
            flex-shrink: 0;
        }

        /* Dynamic feature text */
        .dynamic-val {
            font-weight: 700;
            color: var(--text-main);
            margin: 0 4px;
        }

        .cta-button {
            width: 100%;
            padding: 16px;
            border-radius: 12px;
            font-weight: 600;
            font-size: 1rem;
            cursor: pointer;
            transition: all 0.2s;
            border: none;
        }

        .btn-primary {
            background-color: var(--primary);
            color: white;
        }
        .btn-primary:hover {
            background-color: var(--primary-dark);
        }

        .btn-outline {
            background-color: transparent;
            border: 2px solid #e5e7eb;
            color: var(--text-main);
        }
        .btn-outline:hover {
            border-color: var(--primary);
            color: var(--primary);
        }

        .disabled-overlay {
            position: absolute;
            top: 0; left: 0; right: 0; bottom: 0;
            background: rgba(255, 255, 255, 0.5);
            display: flex;
            align-items: center;
            justify-content: center;
            opacity: 0;
            pointer-events: none;
            transition: opacity 0.3s;
            z-index: 10;
        }
        
        .plan-card.disabled .disabled-overlay {
            opacity: 1;
            pointer-events: all;
        }

        .disabled-msg {
            background: white;
            padding: 15px 25px;
            border-radius: 50px;
            box-shadow: var(--shadow-lg);
            font-weight: 600;
            color: var(--danger);
            text-align: center;
            border: 1px solid #fee2e2;
        }

        @media (max-width: 768px) {
            h1 { font-size: 1.8rem; }
            .pricing-grid { grid-template-columns: 1fr; }
            .plan-card { padding: 1.5rem; }
        }
    </style>
</head>
<body>

    <header>
        <h1>Choose the right plan for your team</h1>
        <p class="subtitle">Scale your resources instantly as your team grows.</p>
        
        <div class="controls-panel">
            <!-- Slider Section -->
            <div class="slider-container">
                <div class="slider-header">
                    <span class="slider-label">Team Size</span>
                    <span class="slider-value" id="teamSizeValue">5 Members</span>
                </div>
                <input type="range" id="teamSizeSlider" min="1" max="50" value="5" step="1">
            </div>

            <!-- Billing Toggle -->
            <div class="billing-toggle">
                <span class="switch-label active" id="labelMonthly">Monthly</span>
                <label class="toggle-switch">
                    <input type="checkbox" id="billingToggle">
                    <span class="slider-round"></span>
                </label>
                <span class="switch-label" id="labelYearly">Yearly</span>
                <span class="discount-badge">Save 20%</span>
            </div>
        </div>
    </header>

    <div class="pricing-grid">
        <!-- STARTER PLAN -->
        <div class="plan-card" id="card-starter">
            <div class="disabled-overlay"><div class="disabled-msg">Not suitable for 10+ users</div></div>
            <div class="plan-header">
                <div class="plan-name">Starter</div>
                <div class="plan-price-wrapper">
                    <span class="currency">$</span>
                    <span class="price" id="price-starter">0</span>
                    <span class="period">/mo</span>
                </div>
                <div class="total-summary" id="total-starter">Total: $0 billed monthly</div>
            </div>
            <ul class="features-list">
                <li><svg fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7"/></svg> Up to <span class="dynamic-val">10</span> users</li>
                <li><svg fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7"/></svg> <span class="dynamic-val" id="storage-starter">10</span> GB Storage</li>
                <li><svg fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7"/></svg> Email Support</li>
            </ul>
            <button class="cta-button btn-outline">Start Free Trial</button>
        </div>

        <!-- GROWTH PLAN (Recommended) -->
        <div class="plan-card recommended" id="card-growth">
            <div class="recommended-badge">Most Popular</div>
            <div class="plan-header">
                <div class="plan-name">Growth</div>
                <div class="plan-price-wrapper">
                    <span class="currency">$</span>
                    <span class="price" id="price-growth">0</span>
                    <span class="period">/mo</span>
                </div>
                <div class="total-summary" id="total-growth">Total: $0 billed monthly</div>
            </div>
            <ul class="features-list">
                <li><svg fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7"/></svg> Unlimited Users</li>
                <li><svg fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7"/></svg> <span class="dynamic-val" id="storage-growth">50</span> GB Storage</li>
                <li><svg fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7"/></svg> <span class="dynamic-val" id="projects-growth">5</span> Active Projects</li>
                <li><svg fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7"/></svg> Priority Email Support</li>
            </ul>
            <button class="cta-button btn-primary">Get Started</button>
        </div>

        <!-- SCALE PLAN -->
        <div class="plan-card" id="card-scale">
            <div class="plan-header">
                <div class="plan-name">Scale</div>
                <div class="plan-price-wrapper">
                    <span class="currency">$</span>
                    <span class="price" id="price-scale">0</span>
                    <span class="period">/mo</span>
                </div>
                <div class="total-summary" id="total-scale">Total: $0 billed monthly</div>
            </div>
            <ul class="features-list">
                <li><svg fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7"/></svg> Unlimited Users</li>
                <li><svg fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7"/></svg> <span class="dynamic-val" id="storage-scale">Unlimited</span> Storage</li>
                <li><svg fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7"/></svg> <span class="dynamic-val" id="projects-scale">Unlimited</span> Active Projects</li>
                <li><svg fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7"/></svg> Dedicated Success Manager</li>
            </ul>
            <button class="cta-button btn-outline">Contact Sales</button>
        </div>
    </div>

    <script>
        // DOM Elements
        const slider = document.getElementById('teamSizeSlider');
        const teamSizeValue = document.getElementById('teamSizeValue');
        const billingToggle = document.getElementById('billingToggle');
        const labelMonthly = document.getElementById('labelMonthly');
        const labelYearly = document.getElementById('labelYearly');

        // Cards
        const cardStarter = document.getElementById('card-starter');
        
        // Plan Data Configuration
        const plans = {
            starter: {
                basePrice: 10, // Per user
                elementId: 'price-starter',
                totalId: 'total-starter',
                features: {
                    storage: { id: 'storage-starter', multiplier: 2 } // 2GB per user
                }
            },
            growth: {
                basePrice: 25, // Per user
                elementId: 'price-growth',
                totalId: 'total-growth',
                features: {
                    storage: { id: 'storage-growth', multiplier: 10 }, // 10GB per user
                    projects: { id: 'projects-growth', multiplier: 2 }  // 2 projects per user
                }
            },
            scale: {
                basePrice: 50, // Per user
                elementId: 'price-scale',
                totalId: 'total-scale',
                features: {
                    // Static features for scale
                }
            }
        };

        function animateValue(obj, start, end, duration) {
            let startTimestamp = null;
            const step = (timestamp) => {
                if (!startTimestamp) startTimestamp = timestamp;
                const progress = Math.min((timestamp - startTimestamp) / duration, 1);
                obj.innerHTML = Math.floor(progress * (end - start) + start);
                if (progress < 1) {
                    window.requestAnimationFrame(step);
                }
            };
            window.requestAnimationFrame(step);
        }

        function updateCalculator() {
            const users = parseInt(slider.value);
            const isYearly = billingToggle.checked;
            
            // 1. Update Header Display
            teamSizeValue.textContent = `${users} ${users === 1 ? 'Member' : 'Members'}`;

            // 2. Update Toggle Labels
            if(isYearly) {
                labelYearly.classList.add('active');
                labelMonthly.classList.remove('active');
            } else {
                labelMonthly.classList.add('active');
                labelYearly.classList.remove('active');
            }

            // 3. Logic for Plan Constraints
            // Example: Starter plan only valid for < 10 users
            if (users > 10) {
                cardStarter.classList.add('disabled');
            } else {
                cardStarter.classList.remove('disabled');
            }

            // 4. Update Prices & Features for each plan
            Object.keys(plans).forEach(planKey => {
                const plan = plans[planKey];
                const priceEl = document.getElementById(plan.elementId);
                const totalEl = document.getElementById(plan.totalId);

                // Calculate Monthly Price Per User (displayed large)
                // If yearly, discount the base price
                let effectiveBasePrice = isYearly ? plan.basePrice * 0.8 : plan.basePrice;
                
                // Usually SaaS shows "Price Per User/Mo", so we display effectiveBasePrice
                // But typically user wants to see Total Cost impact. 
                // Let's display TOTAL Monthly cost in the big number for this specific UX pattern
                // to show "impact", or Per Seat. Let's do Per Seat price and Total Summary.
                
                // Let's actually show the TOTAL price in the big number as requested ("total price instantly")
                let totalMonthlyCost = effectiveBasePrice * users;

                // Animate Price Change if it's a significant jump, otherwise direct update
                // For slider dragging, direct update is smoother
                priceEl.innerText = Math.round(totalMonthlyCost);

                // Update Total Summary Text
                let billPeriodText = isYearly ? 'billed yearly' : 'billed monthly';
                let totalBillAmount = isYearly ? (totalMonthlyCost * 12) : totalMonthlyCost;
                totalEl.innerText = `Total: $${Math.round(totalBillAmount).toLocaleString()} / year equivalent`;
                
                if(!isYearly) {
                    totalEl.innerText = `Total: $${Math.round(totalMonthlyCost).toLocaleString()} / month`;
                }

                // Update Dynamic Features
                if (plan.features) {
                    Object.keys(plan.features).forEach(featureKey => {
                        const feature = plan.features[featureKey];
                        const featureEl = document.getElementById(feature.id);
                        if(featureEl) {
                            // Logic: Base amount + (multiplier * users)
                            let val = feature.multiplier * users;
                            featureEl.innerText = val;
                        }
                    });
                }
            });
        }

        // Event Listeners
        slider.addEventListener('input', updateCalculator);
        billingToggle.addEventListener('change', updateCalculator);

        // Initial Run
        updateCalculator();

    </script>
</body>
</html># democode
