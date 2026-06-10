---
title: "第一个物品"
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

3.运行date来生成相关的json文件，在IDEA的顶端却换即可

4.运行游戏检查代码。如果你成功注册了方块，你应该可以在游戏中看到它了，恭喜你，你已经成功注册了你的第一个物品了，在下一章，我们将介绍如何为您的方块添加轴承旋转等机械动力特有的功能。
