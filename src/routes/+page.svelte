<!--suppress ALL -->
<script lang="ts">
    import {onMount} from 'svelte'
    import {Button, Input} from "@sveltestrap/sveltestrap"
    import {drawText} from "canvas-txt"

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
        level1: HTMLImageElement
        level2: HTMLImageElement
        level3: HTMLImageElement
        level4: HTMLImageElement
        level5: HTMLImageElement
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
        let level: HTMLImageElement | null

        switch (cardState.level) {
            case 0:
                level = null

                break

            case 1:
                level = images.level1

                break

            case 2:
                level = images.level2

                break

            case 3:
                level = images.level3

                break

            case 4:
                level = images.level4

                break

            case 5:
                level = images.level5

                break

            default:
                throw new Error()
        }

        for (let i = 0; i < cardState.level; i++) {
            ctx.drawImage(level!, 8, 124 + i * 32, 32, 32)
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
        const ojplantin = new FontFace("OJ Plantin", "url(OJPlantin-Regular.otf)")

        ojplantin.load().then(f => document.fonts.add(f))

        const baseboost = new Image()
        baseboost.src = "baseboost.png"
        const basebattle = new Image()
        basebattle.src = "basebattle.png"
        const baseevent = new Image()
        baseevent.src = "baseevent.png"
        const basetrap = new Image()
        basetrap.src = "basetrap.png"
        const basegift = new Image()
        basegift.src = "basegift.png"
        const basebanner = new Image()
        basebanner.src = "basebanner.png"
        const basehyper = new Image()
        basehyper.src = "basehyper.png"
        const cost1star = new Image()
        cost1star.src = "cost1star.png"
        const cost10stars = new Image()
        cost10stars.src = "cost10stars.png"
        const costvariablestars = new Image()
        costvariablestars.src = "costvariablestars.png"
        const level1 = new Image()
        level1.src = "level1.png"
        const level2 = new Image()
        level2.src = "level2.png"
        const level3 = new Image()
        level3.src = "level3.png"
        const level4 = new Image()
        level4.src = "level4.png"
        const level5 = new Image()
        level5.src = "level5.png"
        const max1 = new Image()
        max1.src = "max1.png"
        const pudding = new Image()
        pudding.src = "pudding.png"
        const rarecommon = new Image()
        rarecommon.src = "rarecommon.png"
        const rareuncommon = new Image()
        rareuncommon.src = "rareuncommon.png"
        const rarerare = new Image()
        rarerare.src = "rarerare.png"
        const typeboost = new Image()
        typeboost.src = "typeboost.png"
        const typebattle = new Image()
        typebattle.src = "typebattle.png"
        const typeevent = new Image()
        typeevent.src = "typeevent.png"
        const typetrap = new Image()
        typetrap.src = "typetrap.png"
        const typegift = new Image()
        typegift.src = "typegift.png"
        const typebanner = new Image()
        typebanner.src = "typebanner.png"
        const typehyperboost = new Image()
        typehyperboost.src = "typehyperboost.png"
        const typehyperbattle = new Image()
        typehyperbattle.src = "typehyperbattle.png"
        const typehyperevent = new Image()
        typehyperevent.src = "typehyperevent.png"
        const typehypertrap = new Image()
        typehypertrap.src = "typehypertrap.png"
        const typehypergift = new Image()
        typehypergift.src = "typehypergift.png"

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
            level1,
            level2,
            level3,
            level4,
            level5,
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
