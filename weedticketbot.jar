import com.jagrosh.jdautilities.commons.waiter.ButtonWaiter;
import net.dv8tion.jda.api.entities.*;
import net.dv8tion.jda.api.JDABuilder;
import net.dv8tion.jda.api.hooks.ListenerAdapter;
import net.dv8tion.jda.api.events.message.guild.GuildMessageReceivedEvent;
import com.jagrosh.jdautilities.menu.ButtonMenu;

import javax.security.auth.login.LoginException;
import java.util.HashMap;
import java.util.Map;

public class SupportTicketBot extends ListenerAdapter {
    private final String prefix = "!"; // Prefix for bot commands
    private final Map<Long, TextChannel> tickets = new HashMap<>(); // Map to store ticket channels
    private final ButtonWaiter waiter = new ButtonWaiter();
    private final String ownerId = "YOUR_OWNER_ID"; // Replace with your Discord user ID
    private final String roleId = "YOUR_ROLE_ID"; // Replace with your allowed role ID

    public static void main(String[] args) throws LoginException {
        JDABuilder.createDefault("YOUR_BOT_TOKEN")
            .addEventListeners(new SupportTicketBot())
            .build();
    }

    @Override
    public void onGuildMessageReceived(GuildMessageReceivedEvent event) {
        String[] args = event.getMessage().getContentRaw().split("\\s+");
        String command = args[0];

        // Check if the message is sent by the bot owner or has the specified role
        if (!event.getAuthor().getId().equals(ownerId) && !event.getMember().getRoles().stream().anyMatch(role -> role.getId().equals(roleId))) {
            return; // Exit method if the user is not the bot owner or doesn't have the specified role
        }

        if (command.equalsIgnoreCase(prefix + "openticket")) {
            // Check if the command is sent in a text channel
            if (event.isFromType(ChannelType.TEXT)) {
                TextChannel ticketChannel = event.getGuild().createTextChannel("ticket-" + event.getAuthor().getId()).complete();
                ticketChannel.getManager().setParent(event.getGuild().getCategoryById("CATEGORY_ID")).queue(); // Set the category for ticket channels
                ticketChannel.sendMessage("Your support ticket has been opened!").queue();

                // Create a button menu to close the ticket
                ButtonMenu.Builder closeButton = new ButtonMenu.Builder()
                    .setChoices(ButtonMenu.Choice.of("\uD83D\uDD12 Close Ticket"))
                    .setUsers(event.getAuthor())
                    .setTimeout(1, java.util.concurrent.TimeUnit.MINUTES)
                    .setAction(em -> {
                        ticketChannel.delete().queue();
                        tickets.remove(ticketChannel.getIdLong());
                        event.getChannel().sendMessage("Your support ticket has been closed.").queue();
                    });

                waiter.waitForButtonPress(closeButton.build());
                tickets.put(ticketChannel.getIdLong(), ticketChannel);
            }
        }
    }
}
