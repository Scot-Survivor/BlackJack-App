<script>
    import {onMount} from 'svelte';
    import {fade} from 'svelte/transition';
    import PlayerCard from './PlayingCard.svelte';
    import Modal from "./Modal.svelte";

    //https://stackoverflow.com/a/2450976/12921510
    function shuffle(array) {
        var currentIndex = array.length, randomIndex;

        // While there remain elements to shuffle...
        while (0 !== currentIndex) {

            // Pick a remaining element...
            randomIndex = Math.floor(Math.random() * currentIndex);
            currentIndex--;

            // And swap it with the current element.
            [array[currentIndex], array[randomIndex]] = [
                array[randomIndex], array[currentIndex]];
        }

        return array;
    }

    //https://stackoverflow.com/a/39914235/12921510
    function sleep(ms) {
        return new Promise(resolve => setTimeout(resolve, ms));
    }

    let values = {
        '10_of_clubs_white': 10,
        '10_of_diamonds_red': 10,
        '10_of_hearts_red': 10,
        '10_of_spades_white': 10,
        '2_of_clubs_white': 2,
        '2_of_diamonds_red': 2,
        '2_of_hearts_red': 2,
        '2_of_spades_white': 2,
        '3_of_clubs_white': 3,
        '3_of_diamonds_red': 3,
        '3_of_hearts_red': 3,
        '3_of_spades_white': 3,
        '4_of_clubs_white': 4,
        '4_of_diamonds_red': 4,
        '4_of_hearts_red': 4,
        '4_of_spades_white': 4,
        '5_of_clubs_white': 5,
        '5_of_diamonds_red': 5,
        '5_of_hearts_red': 5,
        '5_of_spades_white': 5,
        '6_of_clubs_white': 6,
        '6_of_diamonds_red': 6,
        '6_of_hearts_red': 6,
        '6_of_spades_white': 6,
        '7_of_clubs_white': 7,
        '7_of_diamonds_red': 7,
        '7_of_hearts_red': 7,
        '7_of_spades_white': 7,
        '8_of_clubs_white': 8,
        '8_of_diamonds_red': 8,
        '8_of_hearts_red': 8,
        '8_of_spades_white': 8,
        '9_of_clubs_white': 9,
        '9_of_diamonds_red': 9,
        '9_of_hearts_red': 9,
        '9_of_spades_white': 9,
        'jack_of_clubs_white': 10,
        'jack_of_diamonds_red': 10,
        'ace_of_spades_white': 1,
        'ace_of_diamonds_red': 1,
        'ace_of_hearts_red': 1,
        'ace_of_clubs_white': 1,
        'jack_of_hearts_red': 10,
        'jack_of_spades_white': 10,
        'king_of_clubs_white': 10,
        'king_of_diamonds_red': 10,
        'king_of_hearts_red': 10,
        'king_of_spades_white': 10,
        'queen_of_clubs_white': 10,
        'queen_of_diamonds_red': 10,
        'queen_of_hearts_red': 10,
        'queen_of_spades_white': 10
    }

    let symbols = {'queen': 'Q', 'ace': 'A', 'king': 'K', 'jack': 'J'}

    let aces = ['ace_of_spades_white', 'ace_of_diamonds_red', 'ace_of_hearts_red', 'ace_of_clubs_white']
    let faces = ['jack_of_clubs', 'jack_of_diamonds', 'jack_of_hearts', 'jack_of_spades', 'king_of_clubs', 'king_of_diamonds',
        'king_of_hearts', 'king_of_spades', 'queen_of_clubs', 'queen_of_diamonds', 'queen_of_hearts', 'queen_of_spades']

    class player {
        constructor() {
            this.hand = [];
            this.score = 0;
            this.special_win = false;
            this.bust = false;
        }
        draw (dck) {
            // Randomly select a card.
            dck = shuffle(dck);
            this.hand = this.hand.concat(dck[0]);
            this.calc_score(dck[0])
            // Return the card so we know what to add to discard pile.
            return dck[0];
        }
        calc_score(card) {
            if (this.hand.length === 2) {
                if ((this.hand[0] in aces || this.hand[0] in faces) && (this.hand[1] in aces || this.hand[1] in faces)
                    && !(this.hand[0] in aces && this.hand[1] in aces) && !(this.hand[0] in faces && this.hand[1] in faces)) {
                    this.special_win = true;
                }
            } else {
                if (!(card in aces)) {
                    this.score += values[card];
                } else {
                    if (this.score+11 <= 21) this.score + 11;
                    else this.score +1;
                }
            }
            if (this.score > 21) {
                this.bust = true;
            }
        }
        clear_hand () {
            this.hand = [];
            this.score = 0;
            this.special_win = false;
        }
    }

    const game = {
        win_lose_modal_open: false,
        settings_modal_open: true,
        settings_info: null,
        settings_msg: "",
        game_running: false,
        wins: 0,
        losses: 0,
        won: false,
        lost: false,
        no_of_decks: 0,
        deck: [],
        dealer: null,
        player: null,
        open_close_settings_modal: function () {this.settings_modal_open = !this.settings_modal_open;},
        player_score: function () {
            return this.player.score;
        },
        draw: function (p) {
            this.deck.remove(p.draw(this.deck));
        },
        player_won: function () {
            sleep(2000);  // Sleep for 2 seconds to see they've won.
            this.wins += 1;
            this.win_lose_modal_open = true;
            this.won = true;
        },
        player_lost: function () {
            this.losses += 1;
            this.win_lose_modal_open = true;
            this.lost = true;
        },
        check_win: function () {
            if (!this.player.special_win && !this.dealer.special_win) {
                if (this.dealer.score >= this.player.score) this.player_lost();
                else this.player_won();
            }
            else {
                if (this.player.special_win) {
                    this.player_won();
                } else if (this.dealer.special_win){
                    this.player_lost();
                }
            }
        },
        draw_player: function () {
            this.draw(this.player);
            if (this.player.bust) {
                this.player_lost();
            }
        },
        stand: function () {
            while (this.dealer.score < 17) {
                this.draw(this.dealer);
            }
            if (this.dealer.bust) {
                this.player_won();
            }
            else if (this.dealer.score >= this.player.score) {
                this.player_lost()
            }
            else {
                this.player_won();
            }
        },
        setup: function () {
            // Build up the decks.
            if (this.no_of_decks <= 0) {
                this.settings_msg = "Number of decks must be greater than 0.";
                this.settings_info = true;
                this.settings_modal_open = true;
                return;
            }
            else {
                this.settings_msg = "";
                this.settings_info = false;
            }
            for (let i=0; i<=this.no_of_decks; i++) {
                this.deck = this.deck.concat(Object.keys(values));
            }
            this.game_running = true;
            this.player = new player();
            this.dealer = new player();
            for (let i = 0; i<= 2; i++) {
                this.draw(this.dealer);
                this.draw(this.player);
            }
            if (this.dealer.special_win) {
                this.player_lost();
            } else if (this.player.special_win) {
                this.player_won();
            }
        }
    }

    $: {if (!game.settings_modal_open && !game.game_running) game.setup();}

    // $: {if (!modalOpen) reset()}

    /* onMount(() => {
        setup();
    }) */
</script>

<main>
    <div class="collapse" id="navbarToggleExternalContent">
        <div class="bg-dark p-4">
            <div class="container text-muted">
                <h3 class="text-white text-center"><u>Black Jack Help.</u></h3>
                <br>
                <h4>Objective of the game.</h4>
                <p>Each participant attempts to beat the dealer by getting a count as close to 21 as possible, without going over 21.</p>
                <h4>Card Values & Scoring</h4>
                <p>It is up to each individual player if an ace is worth 1 or 11. Face cards are 10 and any other card is the value shown on the card.</p>
                <h4>The Game!</h4>
                <p>You have <b>2</b> actions in BlackJack. "Hit" & "Stand" if you select "Hit" you're given a card from the deck, if you pick "Stand" it means you stop your turn
                    and it goes to the dealer. If you have a Face & Ace this is called a black jack and you or the dealer wins <b>instantly.</b></p>
            </div>
        </div>
    </div>
    <nav class="navbar navbar-expand-lg navbar-dark bg-dark" style="padding-left: 20px; padding-right: 20px;">
        <div class="navbar-brand">
            <button class="btn btn-outline-info" data-bs-toggle="collapse" data-bs-target="#navbarToggleExternalContent" aria-controls="navbarToggleExternalContent" aria-expanded="false" aria-label="Toggle navigation">
                ?
            </button>
        </div>
        <ul></ul>
        <ul class="navbar-nav">
            <li class="nav-item">
                <h4 class="navbar-text text-center ">
                    <span class="text-success">{game.wins} {game.wins !== 1 ? 'Wins' : 'Win'} </span>
                    <span class="navbar-text text-danger">{game.losses} {game.losses !== 1 ? 'Losses' : 'Loss'}</span>
                </h4>
            </li>
        </ul>
    </nav>
    {#if game.game_running}
        <div class="container-fluid" in:fade={{delay:100}}>
            <div class="row text-center mx-auto">
                <h2 class="text-center white InfoTitle">Dealers cards.</h2>
                <div class="p_cards">
                    {#each game.dealer_hand_to_show as card}
                        <div class="p_card">
                            <PlayerCard>
                                <g class="card" in:fade={{duration: 500}}>
                                    <use href="#card"></use>
                                    <symbol id="{card}" class="{card.split('_')[2]}">
                                        {#if !(card.split('_')[0].charAt(0).toUpperCase() === '1')}
                                            <use href="#{card.split('_')[0].charAt(0).toUpperCase()}"></use>
                                        {:else}
                                            <use href="#10"></use>
                                        {/if}
                                        <use href="#{card.split('_')[2]}"></use>
                                    </symbol>
                                    <use href="#{card}"></use>
                                    <use href="#{card}" transform="translate(215, 310) rotate(180)"></use>
                                </g>
                            </PlayerCard>
                        </div>
                    {/each}
                </div>
                <div class="row text-center">
                    <div class="col">
                        <button style="visibility: hidden">
                        </button>
                    </div>
                    <div class="col">
                        {#if game.show_dealer_score}
                        <span class="text-capitalize Score">{game.dealer_score}</span>
                        {:else}
                            <span class="text-capitalize Score" style="visibility: hidden">{game.dealer_score}</span>
                        {/if}
                    </div>
                    <div class="col">
                        <button style="visibility: hidden">
                        </button>
                    </div>
                </div>
                <br>
                <h2 class="text-center white InfoTitle">Your cards.</h2>
                <div class="row">
                    <div class="p_cards">
                        {#each game.player_hand as card}
                            <div class="p_card">
                                <PlayerCard>
                                    <g class="card" in:fade={{duration: 500}}>
                                        <use href="#card"></use>
                                        <symbol id="{card}" class="{card.split('_')[2]}">
                                            {#if !(card.split('_')[0].charAt(0).toUpperCase() === '1')}
                                                <use href="#{card.split('_')[0].charAt(0).toUpperCase()}"></use>
                                            {:else}
                                                <use href="#10"></use>
                                            {/if}
                                            <use href="#{card.split('_')[2]}"></use>
                                        </symbol>
                                        <use href="#{card}"></use>
                                        <use href="#{card}" transform="translate(215, 310) rotate(180)"></use>
                                    </g>
                                </PlayerCard>
                            </div>
                        {/each}
                    </div>
                </div>
                <div class="row text-center mx-auto">
                    <div class="col">
                        <button type="button" class="btn btn-outline-success text-center" on:click={game.draw_player}>
                            Hit
                        </button>
                    </div>
                    <div class="col">
                        <span class="text-capitalize Score" >
                            <span>{game.player_score}</span>
                        </span>
                    </div>
                    <div class="col">
                        <button type="button" class="btn btn-outline-danger text-center" on:click={game.stand}>
                            Stand
                        </button>
                    </div>
                </div>
            </div>
        </div>
        <Modal text_colour_title="{game.won ? 'text-success': 'text-danger'}" bind:isOpen={game.win_lose_modal_open}>
            <span slot="title">
                {game.won ? 'You Win!' : 'You Lose'}
            </span>
            <span slot="body">
                {game.won ? 'Well done you\'ve won! You have': 'Oh You\'ve lost. You have'} <span class="text-success">{game.wins} {game.wins !== 1 ? 'Wins' : 'Win'} </span> and <span class="text-danger">{game.losses} {game.losses !== 1 ? 'Losses' : 'Loss'}</span>
            </span>
        </Modal>
    {:else}
        <Modal bind:isOpen={game.settings_modal_open}>
            <span slot="title">
                How many decks do you want to play with?
            </span>
            <span slot="body">
                <div class="mb-3">
                        <label for="number-of-decks">
                            Number of decks
                        </label>
                    <input type="number" class="form-control" id="number-of-decks" bind:value={game.no_of_decks}>
                </div>
                {#if game.settings_info !== null}
                    <div class="mb-3 text-danger">
                        <p>{game.settings_msg}</p>
                    </div>
                {/if}
            </span>
        </Modal>
    {/if}
</main>
<style>
    .Score {
        font-weight: bolder;
        color: #a8a4a4;
        font-size: 40px;
        text-align-all: center;
    }
    @media screen and (max-width: 800px) {
        .InfoTitle {
        }
    }
    .white {
        color: white;
    }
    .p_card:hover {
        box-shadow: rgba(0, 0, 0, 0.25) 0 25px 50px -12px;
        transition: all 0.3s ease;
    }
    .p_card{
        padding: 0;
        border: none;
        margin: 0 5px 5px 0;
        box-shadow: 0 15px 10px -10px rgba(31, 31, 31, 0.5);
        transition: all 0.3s ease;
    }
    .p_cards {
        display: flex;
        flex-direction: row;
        justify-content: center;
    }
</style>
