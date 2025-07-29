<script lang="ts">
    import {onMount} from "svelte";
    import {
        getClientInfo
    } from "../../../integration/rest";
    import type {ClientInfo} from "../../../integration/types";
    import {listen} from "../../../integration/ws";

    let currentAnimatedText: string = '';
    let texts: string[] = ["Да, я спиздил бит",
                            "Да, ты топ Европы, парень",
                            "Твой никнейм же dead inside",
                            "Ты ведь так оригинален",
                            "Сколько мы таких видали",
                            "Три рейза, домой, сын твари",
                            "Расскажи-ка своей маме",
                            "Как тебя в лобби наказали",
                            "Вырезаю сукам глотки",
                            "Dead inside, да, бью все шмотки",
                            "Сука, как ни странно, every game одни уёбки",
                            "Почему не жмёшь на кнопки?",
                            "Почему тини в иконке?",
                            "Почему, блядь, за меня играешь, сын уёбка?"]
    let phraseIndex: number = 0;
    let charIndex: number = 0; 
    let isDeleting: boolean = false;
    let typingSpeed: number = 100;
    let clientInfo: ClientInfo | null = null;
    let isLoading: boolean = false;
    let error: string | null = null;
    const REFRESH_INTERVAL_MS = 1000;
    let intervalId: number; 

    async function fetchClientInfo() {
        isLoading = true;
        error = null;

        try {
            clientInfo = await getClientInfo();
        } catch (e) {
            console.error("Failed to fetch client info:", e);
            error = e instanceof Error ? e.message : "Ошибка загрузки данных.";
            clientInfo = null;
        } finally {
            isLoading = false;
        }
    }

    let typingIntervalId: number

    onMount(async () => {
        fetchClientInfo();
        intervalId = setInterval(fetchClientInfo, REFRESH_INTERVAL_MS);
        startTypingAnimation()
    });

    function startTypingAnimation(): void {
        typingIntervalId = setInterval(() => {
            const currentPhrase = texts[phraseIndex];

            if (isDeleting) {
                currentAnimatedText = currentPhrase.substring(0, charIndex - 1);
                charIndex--;

                if (charIndex === 0) {
                    isDeleting = false;
                    phraseIndex = (phraseIndex + 1) % texts.length;
                }
            } else {
                currentAnimatedText = currentPhrase.substring(0, charIndex + 1);
                charIndex++; 
                if (charIndex === currentPhrase.length) {
                    isDeleting = true;
                }
            }
        }, typingSpeed);
    }
</script>

<div class="background">
    <span>еваbounce {clientInfo?.fps}фпс</span>
    <span>{currentAnimatedText}</span>
</div>

<style lang="scss">
    .background {
        height: auto;
        flex-direction: column;
        display: flex;
        align-items: flex-start;
        padding: 1px 5px;
        background-color: #FEFEFE;
        border-radius: 5px;
        font-family: "Pixel"
    }

    .background::before {
        content: '';
        position: absolute;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
        background-color: #000000AA;
        border-radius: 5px;
        transform: translate(-3px, 3px);
        z-index: -1;
    }
</style>
