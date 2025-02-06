package dev.kozlowski.ns_customnpc;

import com.mojang.authlib.GameProfile;
import com.mojang.authlib.properties.Property;
import net.minecraft.network.protocol.game.*;
import net.minecraft.server.MinecraftServer;
import net.minecraft.server.level.EntityPlayer;
import net.minecraft.server.level.WorldServer;
import net.minecraft.server.network.PlayerConnection;
import org.bukkit.Bukkit;
import org.bukkit.Location;
import org.bukkit.craftbukkit.v1_19_R1.CraftServer;
import org.bukkit.craftbukkit.v1_19_R1.CraftWorld;
import org.bukkit.craftbukkit.v1_19_R1.entity.CraftPlayer;
import org.bukkit.entity.Player;

import java.util.Collections;
import java.util.UUID;

public class NPCManager {

    public static void spawnNPC(Player player, Location location) {
        GameProfile profile = new GameProfile(UUID.randomUUID(), "NPC_Test");

        // Skin - Wstaw własne wartości BASE64
        String texture = "ewogICJ0aW1lc3RhbXAiIDogMTY2Mjk5OTYzNDQyMiwKICAicHJvZmlsZUlkIiA6ICI1MTY4ZjZlMjIyM2E0Y2FjYjdiN2QyZjYyZWMxZGFhOSIsCiAgInByb2ZpbGVOYW1lIiA6ICJkZWZfbm90X2FzaCIsCiAgInNpZ25hdHVyZVJlcXVpcmVkIiA6IHRydWUsCiAgInRleHR1cmVzIiA6IHsKICAgICJTS0lOIiA6IHsKICAgICAgInVybCIgOiAiaHR0cDovL3RleHR1cmVzLm1pbmVjcmFmdC5uZXQvdGV4dHVyZS9lYzQ5YTQ0MWY2MWFiZjNkYzQzZTM3NTM5ZWYzYTljNDUxNDBiZjM3NDIxMzlmZjUzNjc4ZTkwMmY1NzM3MTQyIgogICAgfQogIH0KfQ==";
        String signature = "eBbyBw9kfW2kkjyS94klNcHqUbDOU7d7FPdgMpYOK+/WDmVxtqhHYnhHAKyMISZmcIvL/wDuofqqD6UwDUdWI0aQVRcu1v7dg4evQsBcu2hjkSiQ2I2kb8baFWmi1xCyIQVhNxQ7ctklY1SV3Y7srQNOgUC+8DtNOnzjc8PyUMva4h7CFJZ5ZyX3ZjsYWJsFkOzvmRQ/LFfz0c2I0MlPHU25Me4j5L+wNsRIezAbG7nwF0DuxNpKJNNxGXfY5ArX/dzvJHkecGRRdBZiMjqEiTHBCAgb4oxW4anoWFK8uXK769tD9JJIafVzBBtX0UfZD5e694It9RqN3cW48K2fwdda6W1vjjl5yu4dCq6w/KPouZmMPqdxNSd8jGjbes0v9ldognV6mar3tQJVqmq2uT6g7p52EIhF8P9O3rcOZC+DChP7AVgeQX+YY28F2aAsiB6v7pnBrT/mVr3AoIk/DQ2YVPBHI1Nmp0D2ez7dl3z89Uu0wKzClDSJJY8Vfh/B2Yj6lyin08xnaZUkeo/ChMt7n4//7uHJUYL5YM3tgoo3SAc4AIV4CLPm1ReqPyWhvFj0IbMSxVdDvdaJL5Xn4jrHtAW6gc7Orndp9unjFo00i/zfw51c4kryTXxhQBmLipJFv/vt92kuzXrziUALkdYVKnnLHpHCOHnv0+q97Wc=";
        profile.getProperties().put("textures", new Property("textures", texture, signature));

        MinecraftServer server = ((CraftServer) Bukkit.getServer()).getServer();
        WorldServer world = ((CraftWorld) location.getWorld()).getHandle();

        EntityPlayer npc = new EntityPlayer(server, world, profile, null);
        npc.a(location.getX(), location.getY(), location.getZ(), location.getYaw(), location.getPitch());

        PlayerConnection connection = ((CraftPlayer) player).getHandle().b;

        connection.a(new PacketPlayOutPlayerInfo(PacketPlayOutPlayerInfo.EnumPlayerInfoAction.a, Collections.singletonList(npc)));
        connection.a(new PacketPlayOutNamedEntitySpawn(npc));
        connection.a(new PacketPlayOutEntityHeadRotation(npc, (byte) ((location.getYaw() * 256.0F) / 360.0F)));
        connection.a(new PacketPlayOutPlayerInfo(PacketPlayOutPlayerInfo.EnumPlayerInfoAction.d, Collections.singletonList(npc)));

        Bukkit.getScheduler().runTaskLater(Bukkit.getPluginManager().getPlugin("CustomNPC"), () -> {
            connection.a(new PacketPlayOutEntityDestroy(npc.ae()));
            connection.a(new PacketPlayOutNamedEntitySpawn(npc));
        }, 20L);
    }
}
