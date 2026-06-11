---
title: "REGISTRATE注册系统初步"
date: 2026-06-05 10:00:00 +0800
categories: [机械动力教程, 开始制作]
tags: [开始制作, 机械动力教程]
author: golden-train
---

在本节，将正式开始教程，首先，与普通MOD编写不同，您可以使用机械动力内置的注册系统Registrate来进行注册，它为包括方块在内的多种物品提供了统一的注册路径，您可以使用它来简洁的编写代码。1.要调用REGISTRATE您需要先在主类中声明

> 主类即是在软件包下以您的modid命名的类，如果您不清楚什么是软件包，您可以理解成一种特殊的文件夹（它的路径默认是src/main/java/com/您注册时的名字/MOD名字/IntegratedIndustry.java）

    public static final Registrate REGISTRATE = Registrate.create(CreativeMod.MOD_ID);
    static {
        REGISTRATE.defaultCreativeTab(CreativeModeTabs.COMBAT);
        REGISTRATE.setTooltipModifierFactory(item -> new ItemDescription.Modifier(item, FontHelper.Palette.STANDARD_CREATE)
                .andThen(TooltipModifier.mapNull(KineticStats.create(item))));
    }//static后的内容是增加机械动力信息注册的，不想写就可以不要，如果需要建议您紧跟刚才的声明

    public YourMOdsName(IEventBus modEventBus, ModContainer modContainer) {
        modEventBus.addListener(this::commonSetup);
        BLOCKS.register(modEventBus);
        ITEMS.register(modEventBus);
        CREATIVE_MODE_TABS.register(modEventBus);
        NeoForge.EVENT_BUS.register(this);
        modEventBus.addListener(this::addCreative);
        modContainer.registerConfig(ModConfig.Type.COMMON, Config.SPEC);

        REGISTRATE.registerEventListeners(modEventBus);//调用REGISTRATE来进行注册,其它代码是该类自带的，您只需要在该类中添加这一行代码即可。必须添加
    }

这样，您就可以在后续的代码中使用REGISTRATE来进行注册了，接下来，我们将介绍如何使用REGISTRATE来注册一个物品。2.首先，在软件包下创建一个新的类，命名为ModBlocks（命名可以随意，但建议按照这个格式来命名，方便区分），在该类中添加以下代码：

    public class ModBlocks {
        public static final BlockEntry<RotatedPillarBlock> THERMAL_ENERGY_GENERATOR = REGISTRATE
            .block("thermal_energy_generator",RotatedPillarBlock::new)
            .initialProperties(()-> Blocks.OAK_WOOD)//继承方块的属性
            .properties(p -> p.sound(SoundType.HARD_CROP))//单独替代，比如这里把声音替换成了硬作物
            .blockstate(BlockStateGen.axisBlockProvider(true))//机械动力的朝向写法
            .transform(TagGen.axeOrPickaxe())
            .tag(BlockTags.NEEDS_STONE_TOOL)//挖掘的相关tags
            .tag(AllTags.AllBlockTags.VALVE_HANDLES.tag)//机械动力相关标签,这个是需要阀门把手
            .lang("Thremal energy generator")//
            .loot((p, b) -> p.dropOther(b, Items.BIRCH_WOOD))//战利品，掉落的挖掘物
            .simpleItem()//生成一个简单的物品，作为方块的物品形式
            .register();
    }

这样，我们就成功注册了一个方块，接下来，我们将介绍一下代码中的每一行的作用
需要注意，您还需要额外完成json文件的编写，当前阶段您可以求助AI，在后续的教程中，我会介绍如何使用生成json文件的工具来简化这个过程。

再运行之前，本方块还没有导入自动的模型生成，您需要手动完成，放置在
resources/assets/您MOD的名字/models/block/您方块的名字.json
如果没有，请手动创建，确保路径与给出的一致
以下是代码，如果您使用它，则无需额外装载资源图，因为直接调用了MC的资源
    {
    "parent": "minecraft:block/cube_column",
    "textures": {
        "end": "create:block/brass_gearbox",
        "side": "minecraft:block/oak_log"
    }
    }

否则，您还需要再资源文件中放入相关贴图文件，保持与名称一致即可
resources/assets/您MOD的名字/textures/block/您方块的名字.png

3.运行data来生成相关的json文件，在IDEA的顶端却换即可

4.运行游戏检查代码。如果你成功注册了方块，你应该可以在游戏中看到它了，恭喜你，你已经成功注册了你的第一个方块了，在下一章，我们将介绍如何为您的方块添加轴承旋转等机械动力特有的功能。
[![](/assets/img/018.png)](/assets/img/018.png)

>REGISTRATE并非是机械动力的专属功能，它是一个相当简洁的注册工具，可以一次性生成包括模型，语言文件在内的多种json，您甚至无需配置额外的数据生成器。当然，如果您更加熟悉neoforge给出的DeferredRegister，您也可以继续使用它来进行注册，机械动力的功能并不依赖于REGISTRATE，您完全可以在不使用REGISTRATE的情况下编写机械动力MOD。但是，REGISTRATE的确可以让你更轻松的编写代码。这里是REGISTRATE的源代码地址，如果您喜欢，可以为开发人员点个星。[Registrate](https://github.com/tterrag1098/Registrate)