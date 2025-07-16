<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>SF Gems: A Team Guide</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
    <!-- Chosen Palette: Coastal Fog (Warm neutrals with earthy accents) -->
    <!-- Application Structure Plan: The SPA uses a two-part structure: a dynamic, filterable card grid for exploring spots, and a modal form for adding new spots. This design separates the core user tasks of consumption and contribution, making both intuitive. The card grid is more visually engaging and scannable than a static list, while the category filters allow users to quickly find relevant information. This structure was chosen to transform the static source template into a genuinely useful and interactive team tool. -->
    <!-- Visualization & Content Choices: Report Info: Team's favorite spots (qualitative entries). Goal: Organize & Explore. Viz/Presentation Method: Interactive filter buttons and a responsive grid of "spot cards". The form for adding spots is presented in a modal to avoid navigating away from the main view. Interaction: Users can filter the card grid by category (Cafe, Park, etc.) or add new spots, which instantly appear in the grid. Justification: This approach is ideal for Browse a collection of user-generated, non-numeric content. It's a common and highly usable UI pattern that encourages both exploration and contribution. Library/Method: Vanilla JS for DOM manipulation and state, Tailwind CSS for layout and styling. -->
    <!-- CONFIRMATION: NO SVG graphics used. NO Mermaid JS used. -->
    <style>
        body {
            font-family: 'Inter', sans-serif;
            background-color: #F8F7F4;
            color: #3D3D3D;
        }
        .card {
            background-color: #FFFFFF;
            border: 1px solid #EAE8E2;
            transition: transform 0.2s ease-in-out, box-shadow 0.2s ease-in-out;
        }
        .card:hover {
            transform: translateY(-5px);
            box-shadow: 0 10px 15px -3px rgb(0 0 0 / 0.05), 0 4px 6px -4px rgb(0 0 0 / 0.05);
        }
        .btn {
            transition: background-color 0.2s ease-in-out, color 0.2s ease-in-out;
        }
        .filter-btn.active {
            background-color: #D48C70;
            color: #FFFFFF;
            border-color: #D48C70;
        }
        .modal-backdrop {
            background-color: rgba(0, 0, 0, 0.5);
        }
    </style>
</head>
<body class="antialiased">

    <div id="app" class="container mx-auto px-4 sm:px-6 lg:px-8 py-8 md:py-12">

        <header class="text-center mb-8 md:mb-12">
            <h1 class="text-4xl md:text-5xl font-bold text-gray-800 mb-2">🌉 SF Gems & Trails 🌲</h1>
            <p class="text-lg text-gray-600 max-w-3xl mx-auto">Welcome! This is our team's living map of cherished spots in and around San Francisco. Explore contributions or add your own favorite discovery.</p>
        </header>

        <div class="flex flex-col md:flex-row justify-between items-center mb-8">
            <div id="filter-container" class="flex flex-wrap justify-center gap-2 mb-4 md:mb-0">
                 <button data-category="All" class="filter-btn btn active text-sm font-medium py-2 px-4 rounded-full border border-gray-300 bg-white hover:bg-gray-100">All</button>
                 <button data-category="☕ Cafe/Restaurant" class="filter-btn btn text-sm font-medium py-2 px-4 rounded-full border border-gray-300 bg-white hover:bg-gray-100">☕ Cafe/Restaurant</button>
                 <button data-category="🏞️ Park/Viewpoint" class="filter-btn btn text-sm font-medium py-2 px-4 rounded-full border border-gray-300 bg-white hover:bg-gray-100">🏞️ Park/Viewpoint</button>
                 <button data-category="🚶‍♂️ Hiking Trail" class="filter-btn btn text-sm font-medium py-2 px-4 rounded-full border border-gray-300 bg-white hover:bg-gray-100">🚶‍♂️ Hiking Trail</button>
                 <button data-category="🎭 Unique Experience" class="filter-btn btn text-sm font-medium py-2 px-4 rounded-full border border-gray-300 bg-white hover:bg-gray-100">🎭 Unique Experience</button>
                 <button data-category="🛍️ Shop" class="filter-btn btn text-sm font-medium py-2 px-4 rounded-full border border-gray-300 bg-white hover:bg-gray-100">🛍️ Shop</button>
                 <button data-category="🍹 Bar" class="filter-btn btn text-sm font-medium py-2 px-4 rounded-full border border-gray-300 bg-white hover:bg-gray-100">🍹 Bar</button>
            </div>
            <button id="add-spot-btn" class="btn bg-[#5A67D8] hover:bg-[#434190] text-white font-bold py-2 px-6 rounded-lg shadow-md">
                + Add Your Spot
            </button>
        </div>

        <main id="spots-grid" class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-8">
        </main>
    </div>

    <div id="add-spot-modal" class="fixed inset-0 z-50 hidden items-center justify-center p-4">
        <div class="modal-backdrop fixed inset-0"></div>
        <div class="bg-white rounded-lg shadow-2xl p-6 md:p-8 w-full max-w-lg z-10 relative max-h-screen overflow-y-auto">
            <button id="close-modal-btn" class="absolute top-4 right-4 text-gray-400 hover:text-gray-600">
                <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"></path></svg>
            </button>
            <h2 class="text-2xl font-bold mb-6 text-gray-800">Add Your Favorite Spot</h2>
            <form id="spot-form" class="space-y-4">
                <div>
                    <label for="spot-name" class="block text-sm font-medium text-gray-700">Name of Spot/Trail</label>
                    <input type="text" id="spot-name" name="spotName" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-indigo-500 focus:ring-indigo-500" required>
                </div>
                <div>
                    <label for="spot-location" class="block text-sm font-medium text-gray-700">Location (Neighborhood/Area)</label>
                    <input type="text" id="spot-location" name="spotLocation" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-indigo-500 focus:ring-indigo-500" required>
                </div>
                <div>
                    <label for="spot-category" class="block text-sm font-medium text-gray-700">Category</label>
                    <select id="spot-category" name="spotCategory" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-indigo-500 focus:ring-indigo-500" required>
                        <option>☕ Cafe/Restaurant</option>
                        <option>🏞️ Park/Viewpoint</option>
                        <option>🚶‍♂️ Hiking Trail</option>
                        <option>🎭 Unique Experience</option>
                        <option>🛍️ Shop</option>
                        <option>🍹 Bar</option>
                    </select>
                </div>
                <div>
                    <label for="spot-love" class="block text-sm font-medium text-gray-700">Why I Love It</label>
                    <textarea id="spot-love" name="spotLove" rows="3" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-indigo-500 focus:ring-indigo-500" required></textarea>
                </div>
                <div>
                    <label for="spot-tip" class="block text-sm font-medium text-gray-700">Pro Tip/Notes</label>
                    <input type="text" id="spot-tip" name="spotTip" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-indigo-500 focus:ring-indigo-500" required>
                </div>
                <div>
                    <label for="spot-submitter" class="block text-sm font-medium text-gray-700">Your Name/Initials</label>
                    <input type="text" id="spot-submitter" name="spotSubmitter" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-indigo-500 focus:ring-indigo-500" required>
                </div>
                <div class="pt-4">
                    <button type="submit" class="w-full btn bg-[#5A67D8] hover:bg-[#434190] text-white font-bold py-3 px-4 rounded-lg shadow-md">
                        Submit Spot
                    </button>
                </div>
            </form>
        </div>
    </div>

    <script>
        document.addEventListener('DOMContentLoaded', () => {
            const spotsGrid = document.getElementById('spots-grid');
            const addSpotBtn = document.getElementById('add-spot-btn');
            const modal = document.getElementById('add-spot-modal');
            const closeModalBtn = document.getElementById('close-modal-btn');
            const modalBackdrop = document.querySelector('.modal-backdrop');
            const spotForm = document.getElementById('spot-form');
            const filterContainer = document.getElementById('filter-container');

            let spotsData = [
                {
                    name: 'Cafe Aquatica',
                    location: 'Jenner, CA (Sonoma Coast)',
                    category: '☕ Cafe/Restaurant',
                    love: 'A whisper of a cafe, perched where the Russian River kisses the Pacific. The scent of coffee dances with salty air, and live music harmonizes with the ocean\'s lullaby. A serene communion with nature.',
                    tip: 'Go on a weekday afternoon to get a good seat on the deck. The gluten-free pastries are surprisingly amazing!',
                    submitter: 'Team Lead'
                },
                {
                    name: 'The Presidio National Park',
                    location: 'The Presidio',
                    category: '🏞️ Park/Viewpoint',
                    love: 'A massive urban forest escape with stunning views of the Golden Gate Bridge, Alcatraz, and the bay. There\'s a trail for every mood, and the Yoda fountain is a fun surprise!',
                    tip: 'Start near the Warming Hut for a casual bayfront stroll, or head up to Lovers\' Lane for a serene redwood forest walk. Dress in layers!',
                    submitter: 'HR'
                },
                {
                    name: 'Golden Boy Pizza',
                    location: 'North Beach',
                    category: '☕ Cafe/Restaurant',
                    love: 'This isn\'t your typical SF sourdough pizza. It\'s a no-frills, cash-only spot serving thick, rectangular, focaccia-style slices that are just heavenly. The cheese is crispy at the edges and the sauce is perfect.',
                    tip: 'It\'s tiny and there\'s always a line, but it moves fast. Get the clam and garlic slice if you\'re feeling adventurous!',
                    submitter: 'DK'
                },
                 {
                    name: 'Mount Davidson',
                    location: 'Miraloma Park',
                    category: '🚶‍♂️ Hiking Trail',
                    love: 'The highest natural point in San Francisco. It\'s a short, easy hike up through a misty, magical-feeling forest that opens up to incredible panoramic views of the city. Feels like a secret.',
                    tip: 'Much less crowded than Twin Peaks. Perfect for a quick morning hike to clear your head. Can be muddy after rain.',
                    submitter: 'AJ'
                }
            ];

            const renderSpots = (filter = 'All') => {
                spotsGrid.innerHTML = '';
                const filteredSpots = filter === 'All' ? spotsData : spotsData.filter(spot => spot.category === filter);

                if (filteredSpots.length === 0) {
                    spotsGrid.innerHTML = `<div class="md:col-span-2 lg:col-span-3 text-center text-gray-500 py-16">
                        <p class="text-xl">No spots found for this category yet.</p>
                        <p>Be the first to add one!</p>
                    </div>`;
                    return;
                }

                filteredSpots.forEach(spot => {
                    const card = document.createElement('div');
                    card.className = 'card rounded-lg shadow-sm p-6 flex flex-col';
                    card.innerHTML = `
                        <div class="flex-grow">
                            <span class="inline-block bg-gray-100 text-gray-700 text-xs font-semibold px-2.5 py-1 rounded-full mb-3">${spot.category}</span>
                            <h3 class="text-xl font-bold text-gray-800 mb-2">${spot.name}</h3>
                            <p class="text-sm font-medium text-[#D48C70] mb-4">${spot.location}</p>
                            <p class="text-gray-600 mb-4 leading-relaxed">"${spot.love}"</p>
                            <div class="bg-blue-50 border border-blue-200 rounded-lg p-3 text-sm">
                                <strong class="font-semibold text-blue-800">Pro Tip:</strong>
                                <span class="text-blue-700">${spot.tip}</span>
                            </div>
                        </div>
                        <div class="mt-6 pt-4 border-t border-gray-200 text-right">
                            <p class="text-xs text-gray-500">Submitted by: <strong class="font-medium">${spot.submitter}</strong></p>
                        </div>
                    `;
                    spotsGrid.appendChild(card);
                });
            };

            const toggleModal = (show) => {
                if (show) {
                    modal.classList.remove('hidden');
                    modal.classList.add('flex');
                } else {
                    modal.classList.add('hidden');
                    modal.classList.remove('flex');
                }
            };
            
            addSpotBtn.addEventListener('click', () => toggleModal(true));
            closeModalBtn.addEventListener('click', () => toggleModal(false));
            modalBackdrop.addEventListener('click', () => toggleModal(false));
            
            spotForm.addEventListener('submit', (e) => {
                e.preventDefault();
                const formData = new FormData(spotForm);
                const newSpot = {
                    name: formData.get('spotName'),
                    location: formData.get('spotLocation'),
                    category: formData.get('spotCategory'),
                    love: formData.get('spotLove'),
                    tip: formData.get('spotTip'),
                    submitter: formData.get('spotSubmitter')
                };
                spotsData.unshift(newSpot); 
                spotForm.reset();
                toggleModal(false);
                
                const currentFilter = document.querySelector('.filter-btn.active').dataset.category;
                renderSpots(currentFilter);
            });

            filterContainer.addEventListener('click', (e) => {
                if (e.target.matches('.filter-btn')) {
                    document.querySelector('.filter-btn.active').classList.remove('active');
                    e.target.classList.add('active');
                    const category = e.target.dataset.category;
                    renderSpots(category);
                }
            });

            renderSpots();
        });
    </script>
</body>
</html>
