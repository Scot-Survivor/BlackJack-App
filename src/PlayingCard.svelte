<script>

</script>
<svg width="220px" height="320px" class="playingCard" viewBox="0 0 220 320">
    <style>
        symbol.hearts, symbol.diamonds {fill:red; stroke:red;}
        symbol.spades, symbol.clubs {fill:white; stroke:white;}
        rect.background {
            fill:#1A1A1C;
            stroke:black;
            stroke-width:1;
        }
        text.rank {
            stroke:none;
            text-anchor:middle;
            dominant-baseline:middle;
            transform:translate(25px, 25px);
        }
        g.suit {
            fill:none;
            stroke-width:1;
        }
    </style>
    <defs>
        <!-- background -->
        <rect id="card" class="background" x="0" y="0" width="220" height="320" rx="5"/>
        <!-- ranks -->
        <text class="rank" id="A">A</text>
        <text class="rank" id="K">K</text>
        <text class="rank" id="Q">Q</text>
        <text class="rank" id="J">J</text>
        <text class="rank" id="10">10</text>

        <text class="rank" id="9">9</text>
        <text class="rank" id="8">8</text>
        <text class="rank" id="7">7</text>
        <text class="rank" id="6">6</text>

        <text class="rank" id="5">5</text>
        <text class="rank" id="4">4</text>
        <text class="rank" id="3">3</text>
        <text class="rank" id="2">2</text>

        <!-- suits -->
        <g id="hearts" class="suit" transform="translate(25, 54)">
            <polygon points="6,0 -6,0 0,10"/>
        </g>
        <g id="diamonds" class="suit" transform="translate(25, 59)">
            <polygon points="6,0 0,-6 -6,0 0,6"/>
        </g>
        <g id="spades" class="suit" transform="translate(25, 54)">
            <polygon points="6,10 -6,10 0,0"/>
        </g>
        <g id="clubs" class="suit" transform="translate(25, 55)">
            <circle cx="0"  cy="0" r="3"/>
            <circle cx="-4"  cy="8" r="3"/>
            <circle cx="4"  cy="8" r="3"/>
        </g>
    </defs>

    <slot></slot>
</svg>

<style>
    @media screen and (max-width: 800px) {
        .playingCard {
            height: 60%;
            width: 60%;
            font-size: 180%;
        }
    }

</style>