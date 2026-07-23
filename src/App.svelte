<!--suppress ALL -->
<script lang="ts">
    import {onMount} from 'svelte'
    import {Button, Input} from "@sveltestrap/sveltestrap"
    import {drawText} from "canvas-txt"
    import OJPlantinOTF from './lib/assets/OJPlantin-Regular.otf'
    import BaseBoostPNG from './lib/assets/images/baseboost.png'
    import BaseBattlePNG from './lib/assets/images/basebattle.png'
    import BaseEventPNG from './lib/assets/images/baseevent.png'
    import BaseTrapPNG from './lib/assets/images/basetrap.png'
    import BaseGiftPNG from './lib/assets/images/basegift.png'
    import BaseBannerPNG from './lib/assets/images/basebanner.png'
    import BaseHyperPNG from './lib/assets/images/basehyper.png'
    import Cost1StarPNG from './lib/assets/images/cost/1_star.svg'
    import Cost10StarsPNG from './lib/assets/images/cost/10_star.svg'
    import CostVariableStarsPNG from './lib/assets/images/cost/n_star.svg'
    import LevelPNG from './lib/assets/images/level.svg'
    import Max1PNG from './lib/assets/images/max1.png'
    import PuddingPNG from './lib/assets/images/pudding.webp'
    import RareCommonPNG from './lib/assets/images/rarity/C.svg'
    import RareUncommonPNG from './lib/assets/images/rarity/U.svg'
    import RareRarePNG from './lib/assets/images/rarity/R.svg'
    import TypeBoostPNG from './lib/assets/images/type/boost.png'
    import TypeBattlePNG from './lib/assets/images/type/battle.png'
    import TypeEventPNG from './lib/assets/images/type/event.png'
    import TypeTrapPNG from './lib/assets/images/type/trap.png'
    import TypeGiftPNG from './lib/assets/images/type/gift.png'
    import TypeBannerPNG from './lib/assets/images/type/banner.png'
    import TypeHyperBoostPNG from './lib/assets/images/type/hyperboost.png'
    import TypeHyperBattlePNG from './lib/assets/images/type/hyperbattle.png'
    import TypeHyperEventPNG from './lib/assets/images/type/hyperevent.png'
    import TypeHyperTrapPNG from './lib/assets/images/type/hypertrap.png'
    import TypeHyperGiftPNG from './lib/assets/images/type/hypergift.png'

    let canvas: HTMLCanvasElement
    let ctx: CanvasRenderingContext2D

    let images: {
        baseboost: HTMLImageElement
        basebattle: HTMLImageElement
        baseevent: HTMLImageElement
        basetrap: HTMLImageElement
        basegift: HTMLImageElement
        basebanner: HTMLImageElement
        basehyper: HTMLImageElement
        cost1star: HTMLImageElement
        cost10stars: HTMLImageElement
        costvariablestars: HTMLImageElement
        level: HTMLImageElement
        max1: HTMLImageElement
        pudding: HTMLImageElement
        rarecommon: HTMLImageElement
        rareuncommon: HTMLImageElement
        rarerare: HTMLImageElement
        typeboost: HTMLImageElement
        typebattle: HTMLImageElement
        typeevent: HTMLImageElement
        typetrap: HTMLImageElement
        typegift: HTMLImageElement
        typebanner: HTMLImageElement
        typehyperboost: HTMLImageElement
        typehyperbattle: HTMLImageElement
        typehyperevent: HTMLImageElement
        typehypertrap: HTMLImageElement
        typehypergift: HTMLImageElement
    }

    const levelStarRotation = [
        {
            hue: 0,
            saturation: 0
        },
        {
            hue: 1.5,
            saturation: 0.1
        },
        {
            hue: 5.3,
            saturation: 0.5
        },
        {
            hue: 3,
            saturation: 0.5
        },
        {
            hue: 3.7,
            saturation: 0.5
        }
    ]

    const cardLevels = [0, 1, 2, 3, 4, 5]
    const cardCosts = [0, 3, 5, 10, 13, 20, 30, 40, 50, 100]
    const cardTypes = ["Boost", "Battle", "Event", "Trap", "Gift", "Banner"]
    const cardRarities = ["None", "Common", "Uncommon", "Rare"]
    let cardState = $state({
        name: "Pudding",
        description: "Fully restore HP.",
        descriptionSize: 19,
        quote: "\"Hooray!\" ―QP",
        quoteSize: 18,
        max1: false,
        level: 4,
        cost: 0,
        type: "Boost",
        hyper: false,
        rarity: "Rare"
    })

    function draw() {
        // Clear canvas
        ctx.reset()

        // Draw Card Base
        let base: HTMLImageElement

        if (cardState.hyper) {
            base = images.basehyper
        } else {
            switch (cardState.type) {
                case "Boost":
                    base = images.baseboost

                    break

                case "Battle":
                    base = images.basebattle

                    break

                case "Event":
                    base = images.baseevent

                    break

                case "Trap":
                    base = images.basetrap

                    break

                case "Gift":
                    base = images.basegift

                    break

                case "Banner":
                    base = images.basebanner

                    break

                default:
                    throw new Error()
            }
        }

        ctx.drawImage(base, 0, 0)

        // Draw Card Name
        ctx.font = "22px OJ Plantin"
        ctx.textAlign = "center"
        ctx.fillText(cardState.name, 175, 30, 260)

        // Draw Card Rarity
        let rarity: HTMLImageElement | null = null

        switch (cardState.rarity) {
            case "None":
                break

            case "Common":
                rarity = images.rarecommon

                break

            case "Uncommon":
                rarity = images.rareuncommon

                break

            case "Rare":
                rarity = images.rarerare

                break

            default:
                throw new Error()
        }

        if (rarity !== null) {
            ctx.drawImage(rarity, 310, 13, 22, 22)
        }

        // Draw Card Image
        ctx.save()
        ctx.beginPath()
        ctx.roundRect(46, 42, 256, 256, 18)
        ctx.clip()
        ctx.drawImage(images.pudding, 46, 42, 256, 256)
        ctx.restore()

        // Draw Card Level
        let level = images.level

        for (let i = 0; i < cardState.level; i++) {
            ctx.save()
            ctx.filter = `hue-rotate(${levelStarRotation[cardState.level - 1].hue}deg) saturate(${levelStarRotation[cardState.level - 1].saturation + 100}%)`
            ctx.drawImage(level, 8, 124 + i * 32, 32, 32)
            ctx.restore()
        }

        // Draw Card Cost
        switch (cardState.cost) {
            case 0:
                break

            case 3:
            case 5:
                for (let i = 0; i < cardState.cost; i++) {
                    ctx.drawImage(images.cost1star, 308, 198 - i * 32, 32, 32)
                }

                break

            case 13:
                ctx.drawImage(images.cost10stars, 308, 198, 32, 32)

                for (let i = 0; i < 3; i++) {
                    ctx.drawImage(images.cost1star, 308, 166 - i * 32, 32, 32)
                }

                break

            case 10:
            case 20:
            case 30:
            case 40:
            case 50:
                for (let i = 0; i / 10 < cardState.cost / 10; i += 10) {
                    ctx.drawImage(images.cost10stars, 308, 198 - i / 10 * 32, 32, 32)
                }

                break

            case 100:
                for (let i = 0; i < 5; i++) {
                    ctx.drawImage(images.costvariablestars, 308, 198 - i * 32, 32, 32)
                }

                break

            default:
                throw new Error()
        }

        // Draw Card Type
        let type: HTMLImageElement

        if (!cardState.hyper) {
            switch (cardState.type) {
                case "Boost":
                    type = images.typeboost

                    break

                case "Battle":
                    type = images.typebattle

                    break

                case "Event":
                    type = images.typeevent

                    break

                case "Trap":
                    type = images.typetrap

                    break

                case "Gift":
                    type = images.typegift

                    break

                case "Banner":
                    type = images.typebanner

                    break

                default:
                    throw new Error()
            }
        } else {
            switch (cardState.type) {
                case "Boost":
                    type = images.typehyperboost

                    break

                case "Battle":
                    type = images.typehyperbattle

                    break

                case "Event":
                    type = images.typehyperevent

                    break

                case "Trap":
                    type = images.typehypertrap

                    break

                case "Gift":
                    type = images.typehypergift

                    break

                default:
                    throw new Error()
            }
        }

        ctx.drawImage(type, 30, 290)

        // Draw Max 1
        if (cardState.max1) {
            ctx.drawImage(images.max1, 270, 310)
        }

        // Draw Card Description and Quote
        const textAreaX = 22
        const textAreaY = 320
        const textAreaWidth = 306
        const textAreaHeight = 145

        if (cardState.quote.trim() !== "") {
            // Measure heights first without drawing visibly
            ctx.save()
            ctx.beginPath()
            ctx.rect(0, 0, 0, 0)
            ctx.clip()

            const h_desc = drawText(ctx, cardState.description, {
                width: textAreaWidth,
                height: textAreaHeight,
                x: textAreaX,
                y: textAreaY,
                align: "left",
                vAlign: "top",
                font: "OJ Plantin",
                fontSize: cardState.descriptionSize
            }).height

            const h_quote = drawText(ctx, cardState.quote, {
                width: textAreaWidth,
                height: textAreaHeight,
                x: textAreaX,
                y: textAreaY,
                align: "left",
                vAlign: "top",
                font: "OJ Plantin",
                fontSize: cardState.quoteSize,
                fontStyle: "italic"
            }).height
            ctx.restore()

            const totalHeight = h_desc + cardState.quoteSize + h_quote
            const startY = textAreaY + (textAreaHeight - totalHeight) / 2

            drawText(ctx, cardState.description, {
                width: textAreaWidth,
                height: h_desc,
                x: textAreaX,
                y: startY,
                align: "left",
                vAlign: "top",
                font: "OJ Plantin",
                fontSize: cardState.descriptionSize
            })

            drawText(ctx, cardState.quote, {
                width: textAreaWidth,
                height: h_quote,
                x: textAreaX,
                y: startY + h_desc + cardState.quoteSize,
                align: "left",
                vAlign: "top",
                font: "OJ Plantin",
                fontSize: cardState.quoteSize,
                fontStyle: "italic"
            })
        } else {
            drawText(ctx, cardState.description, {
                width: textAreaWidth,
                height: textAreaHeight,
                x: textAreaX,
                y: textAreaY,
                align: "left",
                vAlign: "middle",
                font: "OJ Plantin",
                fontSize: cardState.descriptionSize
            })
        }
    }

    onMount(() => {
        const ojplantin = new FontFace("OJ Plantin", `url(${OJPlantinOTF})`)

        ojplantin.load().then(f => document.fonts.add(f))

        const baseboost = new Image()
        baseboost.src = BaseBoostPNG
        const basebattle = new Image()
        basebattle.src = BaseBattlePNG
        const baseevent = new Image()
        baseevent.src = BaseEventPNG
        const basetrap = new Image()
        basetrap.src = BaseTrapPNG
        const basegift = new Image()
        basegift.src = BaseGiftPNG
        const basebanner = new Image()
        basebanner.src = BaseBannerPNG
        const basehyper = new Image()
        basehyper.src = BaseHyperPNG
        const cost1star = new Image()
        cost1star.src = Cost1StarPNG
        const cost10stars = new Image()
        cost10stars.src = Cost10StarsPNG
        const costvariablestars = new Image()
        costvariablestars.src = CostVariableStarsPNG
        const level = new Image()
        level.src = LevelPNG
        const max1 = new Image()
        max1.src = Max1PNG
        const pudding = new Image()
        pudding.src = PuddingPNG
        const rarecommon = new Image()
        rarecommon.src = RareCommonPNG
        const rareuncommon = new Image()
        rareuncommon.src = RareUncommonPNG
        const rarerare = new Image()
        rarerare.src = RareRarePNG
        const typeboost = new Image()
        typeboost.src = TypeBoostPNG
        const typebattle = new Image()
        typebattle.src = TypeBattlePNG
        const typeevent = new Image()
        typeevent.src = TypeEventPNG
        const typetrap = new Image()
        typetrap.src = TypeTrapPNG
        const typegift = new Image()
        typegift.src = TypeGiftPNG
        const typebanner = new Image()
        typebanner.src = TypeBannerPNG
        const typehyperboost = new Image()
        typehyperboost.src = TypeHyperBoostPNG
        const typehyperbattle = new Image()
        typehyperbattle.src = TypeHyperBattlePNG
        const typehyperevent = new Image()
        typehyperevent.src = TypeHyperEventPNG
        const typehypertrap = new Image()
        typehypertrap.src = TypeHyperTrapPNG
        const typehypergift = new Image()
        typehypergift.src = TypeHyperGiftPNG

        images = {
            baseboost,
            basebattle,
            baseevent,
            basetrap,
            basegift,
            basebanner,
            basehyper,
            cost1star,
            cost10stars,
            costvariablestars,
            level,
            max1,
            pudding,
            rarecommon,
            rareuncommon,
            rarerare,
            typeboost,
            typebattle,
            typeevent,
            typetrap,
            typegift,
            typebanner,
            typehyperboost,
            typehyperbattle,
            typehyperevent,
            typehypertrap,
            typehypergift
        }

        canvas = document.getElementById("preview")! as HTMLCanvasElement
        ctx = canvas.getContext("2d")!
        canvas.width = 349
        canvas.height = 488

        Promise.all(Object.values(images).map(i => new Promise(res => i.onload = res))).then(draw)
    })
</script>

<style>
    .root-container {
        display: flex;
        flex-direction: row;
        justify-content: center;
        align-items: flex-start;
        gap: 40px;
        padding: 20px;
        flex-wrap: wrap;
    }

    @media (max-width: 800px) {
        .root-container {
            flex-direction: column;
            align-items: center;
            gap: 20px;
            padding: 10px;
        }
    }

    .preview-section {
        display: flex;
        flex-direction: column;
        align-items: center;
        gap: 15px;
    }

    .controls-section {
        display: flex;
        flex-direction: column;
        gap: 15px;
        width: 100%;
        max-width: 450px;
    }

    .container {
        display: flex;
        align-items: center;
        gap: 10px;
    }

    .container label {
        min-width: 90px;
        font-weight: bold;
        font-family: RockoUltraFLF, sans-serif;
    }

    canvas {
        max-width: 100%;
        height: auto;
        border-radius: 12px;
        box-shadow: 0 4px 15px rgba(0, 0, 0, 0.1);
        background-color: #f8f9fa;
    }

    h1 {
        text-align: center;
        font-family: RockoUltraFLF, sans-serif;
        margin: 20px 0;
    }
</style>

<h1>100% Orange Juice! Card generator</h1>

<div class="root-container">
    <div class="preview-section">
        <canvas id="preview" width="349" height="488"></canvas>
        <Button color="primary" style="display: flex; justify-self: center;" onclick={() => {
            const url = canvas.toDataURL("image/png")
            const downloadLink = document.createElement('a')
            downloadLink.href = url
            downloadLink.download = `${cardState.name.toLowerCase()}.png`

            document.body.appendChild(downloadLink)
            downloadLink.click()
            document.body.removeChild(downloadLink)
        }}>Download
        </Button>
    </div>

    <div class="controls-section">
        <Input type="file" id="upload-image" onchange={e => {
            const reader = new FileReader()

            reader.onload = event => {
                images.pudding = new Image()
                images.pudding.onload = draw
                // @ts-expect-error
                images.pudding.src = event.target!.result!
            }

            // @ts-expect-error
            if (e.target?.files[0]) {
                // @ts-expect-error
                reader.readAsDataURL(e.target!.files[0])
            }
        }}/>

        <div class="container">
            <label for="name">Name:</label>
            <Input id="name" bind:value={cardState.name} onchange={draw}/>
            <Input id="max1" label="Max1" type="checkbox" bind:checked={cardState.max1} onchange={draw}/>
        </div>

        <div class="container">
            <label for="desc">Description:</label>
            <Input id="desc" type="textarea" bind:value={cardState.description} onchange={draw}/>
        </div>

        <div class="container">
            <label for="descsize">Description text size (pixel):</label>
            <Input id="descsize" type="number" bind:value={cardState.descriptionSize} onchange={draw}/>
        </div>

        <div class="container">
            <label for="quote">Quote:</label>
            <Input id="quote" bind:value={cardState.quote} onchange={draw}/>
        </div>

        <div class="container">
            <label for="quotesize">Quote text size (pixel):</label>
            <Input id="quotesize" type="number" bind:value={cardState.quoteSize} onchange={draw}/>
        </div>

        <div class="container">
            <label for="level">Level:</label>
            <Input id="level" type="select" bind:value={cardState.level} onchange={draw}>
                {#each cardLevels as cl}
                    <option value={cl}>
                        {cl.toString()}
                    </option>
                {/each}
            </Input>
        </div>

        <div class="container">
            <label for="cost">Cost:</label>
            <Input id="cost" type="select" bind:value={cardState.cost} onchange={draw}>
                {#each cardCosts as cc}
                    <option value={cc}>
                        {#if cc !== 100}
                            {cc.toString()}
                        {:else}
                            Variable
                        {/if}
                    </option>
                {/each}
            </Input>
        </div>

        <div class="container">
            <label for="type">Type:</label>
            <Input id="type" type="select" bind:value={cardState.type} onchange={e => {
            if (e.currentTarget.value === "Banner") {
                cardState.hyper = false
            }

            draw()
        }}>
                {#each cardTypes as ct}
                    <option value={ct}>
                        {ct}
                    </option>
                {/each}
            </Input>
            <Input type="checkbox" label="Hyper" bind:checked={cardState.hyper} disabled={cardState.type === "Banner"}
                   onchange={draw}/>
        </div>

        <div class="container">
            <label for="rarity">Rarity:</label>
            <Input id="rarity" type="select" bind:value={cardState.rarity} onchange={draw}>
                {#each cardRarities as cr}
                    <option value={cr}>
                        {cr}
                    </option>
                {/each}
            </Input>
        </div>
    </div>
</div>
